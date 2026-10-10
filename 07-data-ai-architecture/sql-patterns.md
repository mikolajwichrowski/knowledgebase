# SQL Patterns for Analytics Engineering

> **TL;DR** — Most analytics problems reduce to a small set of patterns: window functions (ranking, running totals, lag/lead), CTEs for readability, recursive CTEs for hierarchies, gaps-and-islands for sessions/streaks, deduplication with `ROW_NUMBER`, idempotent loads with `MERGE` or partition overwrite, and physical design (indexes, partitioning, clustering) guided by query plans. Snippets below use ANSI-ish SQL; dialect notes for T-SQL (SQL Server/Fabric Warehouse), Spark SQL, BigQuery, Postgres, Snowflake, DuckDB where they differ.

---

## 1. Logical query processing order

```
FROM / JOIN → WHERE → GROUP BY → HAVING → window functions → SELECT → DISTINCT → ORDER BY → LIMIT/TOP
```

Consequences:
- You cannot reference a `SELECT` alias in `WHERE` (except some engines like Snowflake/DuckDB/BigQuery-partially). Use a CTE.
- Window functions cannot be filtered in `WHERE` → wrap in CTE, or use `QUALIFY` (Snowflake, BigQuery, Databricks, DuckDB, Teradata; not T-SQL/Postgres).
- `WHERE` filters rows before aggregation; `HAVING` after.

---

## 2. CTEs

```sql
WITH orders_2026 AS (
    SELECT order_id, customer_id, order_date, net_amount
    FROM silver.sales_order
    WHERE order_date >= DATE '2026-01-01'
),
customer_totals AS (
    SELECT customer_id, SUM(net_amount) AS revenue, COUNT(*) AS orders
    FROM orders_2026
    GROUP BY customer_id
)
SELECT c.customer_name, t.revenue, t.orders, t.revenue / NULLIF(t.orders, 0) AS avg_order_value
FROM customer_totals t
JOIN gold.dim_customer c ON c.customer_id = t.customer_id AND c.is_current = 1
ORDER BY t.revenue DESC;
```

Style (dbt convention): import CTEs at top (`with source as (select * from ...)`), then logical CTEs, final `select * from final`. CTEs are usually inlined by optimisers (not materialised) — Postgres ≥12 inlines unless `MATERIALIZED`; T-SQL always inlines (repeated references are re-evaluated → consider temp tables for heavy reuse).

---

## 3. Window functions

Syntax: `func(...) OVER (PARTITION BY ... ORDER BY ... ROWS|RANGE BETWEEN ... AND ...)`

| Category | Functions |
|---|---|
| Ranking | `ROW_NUMBER`, `RANK` (gaps on ties), `DENSE_RANK` (no gaps), `NTILE(n)`, `PERCENT_RANK`, `CUME_DIST` |
| Offset | `LAG(col, n, default)`, `LEAD`, `FIRST_VALUE`, `LAST_VALUE`, `NTH_VALUE` |
| Aggregate | `SUM`, `AVG`, `COUNT`, `MIN`, `MAX` over a window |

```sql
SELECT
  customer_id,
  order_date,
  net_amount,
  ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date)            AS order_seq,
  SUM(net_amount) OVER (PARTITION BY customer_id ORDER BY order_date
                        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)      AS running_revenue,
  AVG(net_amount) OVER (PARTITION BY customer_id ORDER BY order_date
                        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW)              AS moving_avg_3,
  LAG(order_date) OVER (PARTITION BY customer_id ORDER BY order_date)          AS prev_order_date,
  DATEDIFF(day, LAG(order_date) OVER (PARTITION BY customer_id ORDER BY order_date), order_date) AS days_since_prev,
  net_amount / SUM(net_amount) OVER (PARTITION BY customer_id)                 AS share_of_customer_total
FROM silver.sales_order;
```

**Frame pitfalls**
- Default frame with `ORDER BY` is `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` → ties (same date) are included together; use `ROWS` for deterministic running totals.
- `LAST_VALUE` with default frame returns the current row; specify `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`.
- `ROW_NUMBER` with non-unique `ORDER BY` is non-deterministic → add tie-breaker columns.

### Top-N per group

```sql
-- Top 3 products per category by revenue
WITH ranked AS (
  SELECT category, product, revenue,
         DENSE_RANK() OVER (PARTITION BY category ORDER BY revenue DESC) AS rnk
  FROM gold.product_revenue
)
SELECT * FROM ranked WHERE rnk <= 3;

-- With QUALIFY (Snowflake/BigQuery/Databricks/DuckDB)
SELECT category, product, revenue
FROM gold.product_revenue
QUALIFY DENSE_RANK() OVER (PARTITION BY category ORDER BY revenue DESC) <= 3;
```

### Pareto / ABC classification

```sql
WITH r AS (
  SELECT item_no, SUM(net_amount) AS revenue
  FROM gold.fct_sales_lines WHERE order_date >= DATEADD(year, -1, CAST(GETDATE() AS date))
  GROUP BY item_no
), c AS (
  SELECT item_no, revenue,
         SUM(revenue) OVER (ORDER BY revenue DESC, item_no ROWS UNBOUNDED PRECEDING)
           / SUM(revenue) OVER () AS cum_share
  FROM r
)
SELECT item_no, revenue, cum_share,
       CASE WHEN cum_share <= 0.80 THEN 'A' WHEN cum_share <= 0.95 THEN 'B' ELSE 'C' END AS abc_class
FROM c;
```

### Period-over-period with LAG

```sql
WITH m AS (
  SELECT DATE_TRUNC('month', order_date) AS month, SUM(net_amount) AS revenue
  FROM gold.fct_sales_lines GROUP BY 1
)
SELECT month, revenue,
       LAG(revenue, 12) OVER (ORDER BY month) AS revenue_py,
       revenue / NULLIF(LAG(revenue, 12) OVER (ORDER BY month), 0) - 1 AS yoy_growth
FROM m;
```
(Assumes no missing months — join to a calendar spine first.)

---

## 4. Date spine / calendar

```sql
-- Postgres / DuckDB
SELECT d::date AS date FROM generate_series(DATE '2020-01-01', DATE '2030-12-31', INTERVAL 1 DAY) AS t(d);

-- T-SQL (SQL Server 2022+/Fabric Warehouse: GENERATE_SERIES)
SELECT DATEADD(day, value, '2020-01-01') AS [date]
FROM GENERATE_SERIES(0, DATEDIFF(day, '2020-01-01', '2030-12-31'));

-- BigQuery
SELECT d AS date FROM UNNEST(GENERATE_DATE_ARRAY('2020-01-01', '2030-12-31')) AS d;
```

dbt: `dbt_utils.date_spine`. Use spines to fill missing periods (zero sales days) before LAG/moving averages.

---

## 5. Recursive CTEs (hierarchies, BOMs)

```sql
-- Bill of materials explosion with quantity multiplication
WITH RECURSIVE bom_explode AS (       -- T-SQL: omit RECURSIVE keyword
    SELECT parent_item, component_item, qty_per AS total_qty, 1 AS lvl,
           CAST(parent_item || '>' || component_item AS VARCHAR(4000)) AS path
    FROM silver.bom
    WHERE parent_item = 'FG-1000'
  UNION ALL
    SELECT b.parent_item, c.component_item, b.total_qty * c.qty_per, b.lvl + 1,
           b.path || '>' || c.component_item
    FROM bom_explode b
    JOIN silver.bom c ON c.parent_item = b.component_item
    WHERE b.lvl < 20                                   -- cycle/explosion guard
      AND b.path NOT LIKE '%' || c.component_item || '%' -- naive cycle check
)
SELECT component_item, SUM(total_qty) AS qty_required
FROM bom_explode
GROUP BY component_item;
```

Org/GL hierarchy flattening: recursive CTE producing `level1..levelN` columns, then pivot, for Power BI (which prefers flattened hierarchies; `PATH()` DAX function exists for parent-child too). T-SQL default `MAXRECURSION` is 100; set `OPTION (MAXRECURSION 1000)`.

---

## 6. Deduplication patterns

```sql
-- Keep latest record per business key (most common)
WITH ranked AS (
  SELECT *,
         ROW_NUMBER() OVER (PARTITION BY customer_no
                            ORDER BY modified_at DESC, _ingested_at DESC) AS rn
  FROM bronze.erp_customer
)
SELECT * FROM ranked WHERE rn = 1;

-- Find duplicates
SELECT vat_number, COUNT(*) AS n, STRING_AGG(customer_no, ', ') AS customers
FROM silver.customer
GROUP BY vat_number
HAVING COUNT(*) > 1;

-- Exact duplicate rows (all columns) – hash compare
SELECT row_hash, COUNT(*) FROM (
  SELECT HASHBYTES('SHA2_256', CONCAT_WS('|', col1, col2, col3)) AS row_hash FROM t
) x GROUP BY row_hash HAVING COUNT(*) > 1;

-- Delete duplicates in place (T-SQL)
WITH d AS (
  SELECT ROW_NUMBER() OVER (PARTITION BY order_line_id ORDER BY _ingested_at DESC) AS rn
  FROM silver.sales_lines
)
DELETE FROM d WHERE rn > 1;
```

`CONCAT_WS` with NULLs: NULL is skipped in T-SQL → `('a', NULL, 'b')` and `('a', 'b', NULL)` collide. Coalesce to a sentinel: `COALESCE(col, '~')`.

---

## 7. Gaps and islands

Classic trick: `date - ROW_NUMBER()` is constant within a consecutive run.

```sql
-- Consecutive days a machine was down (islands)
WITH d AS (
  SELECT machine_id, down_date,
         DATEADD(day, -ROW_NUMBER() OVER (PARTITION BY machine_id ORDER BY down_date), down_date) AS grp
  FROM silver.machine_down_days
)
SELECT machine_id, MIN(down_date) AS start_date, MAX(down_date) AS end_date, COUNT(*) AS days
FROM d
GROUP BY machine_id, grp;
```

Session-based islands with a gap threshold (e.g. new session after 30 min inactivity):

```sql
WITH e AS (
  SELECT user_id, event_ts,
         CASE WHEN DATEDIFF(minute, LAG(event_ts) OVER (PARTITION BY user_id ORDER BY event_ts), event_ts) > 30
                   OR LAG(event_ts) OVER (PARTITION BY user_id ORDER BY event_ts) IS NULL
              THEN 1 ELSE 0 END AS is_new_session
  FROM silver.events
), s AS (
  SELECT *, SUM(is_new_session) OVER (PARTITION BY user_id ORDER BY event_ts ROWS UNBOUNDED PRECEDING) AS session_no
  FROM e
)
SELECT user_id, session_no, MIN(event_ts) AS session_start, MAX(event_ts) AS session_end, COUNT(*) AS events
FROM s GROUP BY user_id, session_no;
```

Gaps (missing sequence numbers, e.g. invoice numbering compliance):

```sql
SELECT invoice_no + 1 AS gap_start, next_no - 1 AS gap_end
FROM (SELECT invoice_no, LEAD(invoice_no) OVER (ORDER BY invoice_no) AS next_no FROM silver.invoice) x
WHERE next_no - invoice_no > 1;
```

Status-change islands (collapse consecutive identical statuses into intervals): compute `CASE WHEN status <> LAG(status) THEN 1 END`, running sum as group id, then `MIN/MAX` per group — same pattern as sessions.

---

## 8. Idempotent loads

**Idempotent** = running the same load twice for the same input yields the same target state. Essential for retries and backfills.

| Pattern | How | When |
|---|---|---|
| Full refresh | `CREATE OR REPLACE TABLE ... AS SELECT` / truncate-insert | Small/medium tables |
| Partition overwrite | Delete + insert (or `INSERT OVERWRITE`) the date partition(s) being processed | Event/fact tables partitioned by date |
| MERGE / upsert by key | `MERGE` on business key | Mutable entities, CDC |
| Append with dedup | Append-only bronze + dedup view/silver | Raw landing |
| Anti-join insert | Insert rows whose key does not exist | Immutable events |

Anti-patterns: `INSERT INTO target SELECT * FROM source WHERE date = yesterday` (rerun → duplicates); `GETDATE()` inside load logic (non-deterministic) — pass the logical run date as parameter.

### MERGE (T-SQL / Fabric Warehouse / Spark SQL Delta / Snowflake / BigQuery)

```sql
MERGE INTO silver.item AS t
USING (
    SELECT item_no, description, item_group, unit_cost, modified_at
    FROM (
      SELECT *, ROW_NUMBER() OVER (PARTITION BY item_no ORDER BY modified_at DESC) rn
      FROM bronze.erp_item WHERE _batch_id = @batch_id
    ) x WHERE rn = 1                       -- MERGE source must be unique per key!
) AS s
ON t.item_no = s.item_no
WHEN MATCHED AND s.modified_at > t.modified_at THEN
    UPDATE SET description = s.description, item_group = s.item_group,
               unit_cost = s.unit_cost, modified_at = s.modified_at
WHEN NOT MATCHED BY TARGET THEN
    INSERT (item_no, description, item_group, unit_cost, modified_at)
    VALUES (s.item_no, s.description, s.item_group, s.unit_cost, s.modified_at);
-- T-SQL only: WHEN NOT MATCHED BY SOURCE THEN DELETE  (only with full source snapshots!)
```

Postgres upsert: `INSERT ... ON CONFLICT (item_no) DO UPDATE SET description = EXCLUDED.description WHERE item.modified_at < EXCLUDED.modified_at;`

Partition overwrite (Spark/Delta):

```sql
INSERT OVERWRITE silver.sales_lines PARTITION (order_date = '2026-10-09')
SELECT ... FROM bronze.sales_lines WHERE order_date = '2026-10-09';
-- or DataFrame: df.write.mode("overwrite").option("replaceWhere", "order_date = '2026-10-09'").saveAsTable(...)
```

### Soft vs hard deletes from full snapshots

```sql
-- Mark rows missing in today's full extract as deleted
UPDATE t SET is_deleted = 1, deleted_at = @run_ts
FROM silver.customer t
WHERE NOT EXISTS (SELECT 1 FROM bronze.customer_snapshot s
                  WHERE s.customer_no = t.customer_no AND s._snapshot_date = @run_date)
  AND t.is_deleted = 0;
```
Guard: abort if the snapshot row count drops > X% vs previous (protects against partial extracts deleting everything).

---

## 9. Other useful patterns

```sql
-- Conditional aggregation / pivot
SELECT customer_id,
       SUM(CASE WHEN channel = 'webshop' THEN net_amount ELSE 0 END) AS webshop_rev,
       SUM(CASE WHEN channel = 'edi'     THEN net_amount ELSE 0 END) AS edi_rev,
       COUNT(DISTINCT CASE WHEN returned = 1 THEN order_id END)     AS returned_orders
FROM gold.fct_sales_lines GROUP BY customer_id;

-- Point-in-time join to SCD2 / exchange rates (latest rate on or before date)
SELECT s.*, r.rate
FROM sales s
OUTER APPLY (SELECT TOP 1 rate FROM fx_rate r
             WHERE r.currency = s.currency AND r.rate_date <= s.order_date
             ORDER BY r.rate_date DESC) r;            -- T-SQL; LATERAL in Postgres
-- Spark/BigQuery alternative: range join with ROW_NUMBER, or ASOF JOIN (DuckDB, Snowflake)

-- DuckDB / Snowflake ASOF JOIN
SELECT s.*, r.rate FROM sales s ASOF JOIN fx_rate r
  ON s.currency = r.currency AND s.order_date >= r.rate_date;

-- Median / percentiles
SELECT PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY lead_time_days) AS median_lead_time FROM ...;

-- Safe division, null handling
SELECT revenue / NULLIF(qty, 0), COALESCE(discount, 0) FROM ...;

-- Semi-join vs IN with NULLs: NOT IN fails if subquery returns NULL → use NOT EXISTS
SELECT * FROM item i WHERE NOT EXISTS (SELECT 1 FROM sales s WHERE s.item_no = i.item_no);
```

Fan-out trap: joining two one-to-many children of the same parent multiplies rows → aggregate each child first, then join.

---

## 10. Physical design: query plans, indexing, partitioning, clustering

### 10.1 Reading query plans

| Engine | Command |
|---|---|
| SQL Server / Azure SQL / Fabric Warehouse | Actual execution plan (SSMS), `SET STATISTICS IO, TIME ON`, Query Store; Fabric: query insights views |
| Postgres | `EXPLAIN (ANALYZE, BUFFERS) SELECT ...` |
| Spark | `df.explain("formatted")`, Spark UI (stages, shuffle, skew) |
| BigQuery | Execution details tab: bytes processed, slot time, stages |
| Snowflake | Query Profile: partitions scanned vs total, spilling |
| DuckDB | `EXPLAIN ANALYZE` |

What to look for: full scans where seeks expected; estimated vs actual row mismatches (stale statistics); expensive sorts/hash spills to disk; shuffles/exchanges (Spark); data skew (one task much longer); partitions scanned ratio; implicit conversions blocking index use (`WHERE varchar_col = 123`); functions on filtered columns (`WHERE YEAR(order_date) = 2026` is non-sargable → `WHERE order_date >= '2026-01-01' AND order_date < '2027-01-01'`).

### 10.2 Indexing (row stores: SQL Server, Postgres)

| Index | Use |
|---|---|
| Clustered (SQL Server) / primary key | Physical order; choose narrow, ever-increasing key (identity/date) |
| Nonclustered B-tree | Selective filters and joins; **covering** with `INCLUDE` columns |
| Composite | Order columns by equality filters first, then range; leftmost prefix rule |
| Filtered / partial | `WHERE is_current = 1` — small, targeted |
| **Columnstore** (SQL Server/Azure SQL) | Analytics on large tables: compression + batch mode |
| GIN / GiST (Postgres) | JSONB, full-text, arrays, geospatial |
| HNSW / IVFFlat (pgvector) | Vector similarity — see [rag-architecture.md](rag-architecture.md) |
| BRIN (Postgres) | Huge append-only tables naturally ordered by time |

Cost: each index slows writes and uses storage. Index the queries you run (Query Store / `pg_stat_statements`), drop unused indexes.

### 10.3 Partitioning and clustering (analytical engines)

| Technique | Engine examples | Guidance |
|---|---|---|
| Partitioning | BigQuery (date/integer range, ingestion time), Delta/Hive (folder per value), Iceberg (hidden, transform-based), SQL Server partitioned tables | Partition by low-cardinality column used in nearly every filter (date). Aim for partitions ≥ ~1 GB in lakes; avoid thousands of tiny partitions |
| Clustering / sorting | BigQuery clustering (up to 4 cols), Snowflake clustering keys (automatic micro-partitions), Delta Z-order / liquid clustering, Iceberg sort order | Columns frequently filtered/joined with higher cardinality (customer_id, item_no) |
| Distribution | Synapse dedicated pools (hash/round-robin/replicate) | Hash on large-table join key; replicate small dims |
| File compaction | Delta `OPTIMIZE`, Iceberg `rewrite_data_files` | Fix small-file problem |
| Statistics | `UPDATE STATISTICS`, `ANALYZE` | Keep optimiser estimates accurate |

---

## 11. SQL style & testing checklist

- [ ] Explicit column lists; no `SELECT *` in production marts (except import CTEs).
- [ ] Explicit `JOIN` types; comment intentional `LEFT JOIN`s that should not drop rows.
- [ ] Grain stated at top of each model; uniqueness tested.
- [ ] Deterministic ordering in window functions.
- [ ] Half-open date intervals `[start, end)`.
- [ ] Timezones: store UTC + local date column for reporting; be explicit about `AT TIME ZONE`.
- [ ] Decimals for money (`DECIMAL(19,4)`), never floats.
- [ ] Lint with SQLFluff; consistent case and indentation.
- [ ] Row-count and sum reconciliation tests vs source.

## Common mistakes

- Using `DISTINCT` to hide a fan-out join instead of fixing the grain.
- `NOT IN` with nullable subquery returning zero rows.
- `COUNT(column)` vs `COUNT(*)` confusion (NULLs not counted).
- `BETWEEN` with timestamps (`<= '2026-10-31'` misses the last day's times).
- Integer division in some engines (`1/2 = 0` in T-SQL/Postgres integers).
- MERGE with non-unique source keys (error in T-SQL/Delta, or nondeterministic updates).
- Window frame defaults with `LAST_VALUE`.
- Non-idempotent incremental loads.

## References

- Use The Index, Luke (indexing for developers): https://use-the-index-luke.com/
- Modern SQL (window functions, standards): https://modern-sql.com/
- PostgreSQL — Window functions tutorial: https://www.postgresql.org/docs/current/tutorial-window.html
- PostgreSQL — WITH queries (CTEs): https://www.postgresql.org/docs/current/queries-with.html
- T-SQL MERGE: https://learn.microsoft.com/sql/t-sql/statements/merge-transact-sql
- T-SQL OVER clause: https://learn.microsoft.com/sql/t-sql/queries/select-over-clause-transact-sql
- BigQuery partitioned tables: https://cloud.google.com/bigquery/docs/partitioned-tables
- BigQuery clustered tables: https://cloud.google.com/bigquery/docs/clustered-tables
- Delta Lake docs: https://docs.delta.io/
- DuckDB SQL docs: https://duckdb.org/docs/
- SQLFluff: https://sqlfluff.com/
- dbt-utils package: https://github.com/dbt-labs/dbt-utils
- Sibling: [dimensional-modeling.md](dimensional-modeling.md), [modern-data-stack.md](modern-data-stack.md); [Engineering](../08-engineering/README.md)
