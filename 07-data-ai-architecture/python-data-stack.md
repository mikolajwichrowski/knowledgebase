# Python Data Stack

> **TL;DR** — Use SQL for set-based transformation in the warehouse; use Python for ingestion from APIs, complex logic, ML, LLM orchestration, and glue. Pick the dataframe library by data size and runtime: **pandas** for small data and ecosystem compatibility, **Polars** for fast single-node work (lazy, multi-threaded), **DuckDB** when SQL on files is simplest, **PySpark** when data or platform (Fabric/Databricks) demands distributed compute. Validate at boundaries with **pydantic**, talk to databases with **SQLAlchemy**, and move notebook prototypes into tested, packaged pipelines before production.

---

## 1. Library selection

| Library | Model | Sweet spot | Strengths | Limits |
|---|---|---|---|---|
| **pandas** (2.x, Arrow-backed dtypes optional) | Eager, single-threaded, in-memory | < ~1–5 GB, exploration, sklearn/plotting interop | Ubiquitous, huge ecosystem | Memory hungry (5–10× data size), slow loops, index quirks |
| **Polars** | Eager + **lazy** query engine, Rust, Arrow, multi-threaded, streaming | 1–100+ GB on one machine | Very fast, expressive, query optimiser, low memory | Smaller ecosystem (improving), different API |
| **DuckDB** | In-process SQL OLAP | SQL over Parquet/CSV/Delta/Iceberg, dataframes | Zero-copy with pandas/Polars/Arrow, fast | Single node (MotherDuck for cloud) |
| **PySpark** | Distributed, lazy | 100 GB → PB, Fabric/Databricks/Synapse | Scale, Delta native, streaming | Cluster overhead, startup latency, cost; overkill for small data |
| **PyArrow** | Columnar memory format & IO | Interchange, Parquet IO | Standard memory format | Low-level |
| Dask / Ray | Parallel Python | Scale pandas-like / ML workloads | | Operational complexity |
| **SQLAlchemy** | DB toolkit + ORM | Connecting to databases, generating SQL | Dialects for all major DBs | ORM is for apps, not bulk analytics |
| **pydantic** (v2) | Data validation via type hints | API payloads, configs, LLM structured outputs, contracts | Fast (Rust core), JSON Schema generation | Row-wise; not for millions of rows (use pandera/Polars schemas) |
| pandera | DataFrame schema validation | Validating pandas/Polars/PySpark frames | Declarative checks | |
| scikit-learn, statsmodels, LightGBM/XGBoost | ML | See [ml-fundamentals-mlops.md](ml-fundamentals-mlops.md) | | |

Rule of thumb: if the transformation is a join/aggregate on tables already in the warehouse, write SQL (dbt). Pull into Python only what SQL cannot express well.

---

## 2. pandas essentials

```python
import pandas as pd

df = pd.read_parquet("sales_lines.parquet", dtype_backend="pyarrow")

# Method chaining, explicit dtypes, avoid inplace
monthly = (
    df.assign(order_month=lambda d: d["order_date"].dt.to_period("M").dt.to_timestamp(),
              net=lambda d: d["qty"] * d["unit_price"] * (1 - d["discount"].fillna(0)))
      .query("status != 'cancelled'")
      .groupby(["order_month", "item_group"], as_index=False)
      .agg(revenue=("net", "sum"), orders=("order_id", "nunique"))
      .sort_values(["order_month", "revenue"], ascending=[True, False])
)

# Window-like ops
df["running_qty"] = df.sort_values("order_date").groupby("customer_id")["qty"].cumsum()
df["rank_in_group"] = df.groupby("item_group")["revenue"].rank(method="dense", ascending=False)

# Dedup: keep latest per key
latest = df.sort_values("modified_at").drop_duplicates("customer_no", keep="last")

# Merge with validation of cardinality
out = df.merge(dim_item, on="item_no", how="left", validate="many_to_one", indicator=True)
assert (out["_merge"] == "both").all(), "Orphan item_no values"
```

Performance tips: vectorise (no `iterrows`/`apply` with Python functions on big frames); categorical dtype for low-cardinality strings; read only needed columns (`columns=[...]`); use Arrow-backed strings; `pd.eval` rarely needed — switch to Polars/DuckDB when slow.

Common pitfalls: `SettingWithCopyWarning` (use `.loc` or copy-on-write mode, default in pandas 3.0), silent dtype changes (int → float when NaN introduced; use nullable `Int64`), timezone-naive vs aware datetimes, chained indexing.

---

## 3. Polars essentials

```python
import polars as pl

lf = (
    pl.scan_parquet("lake/silver/sales_lines/*.parquet")       # lazy: nothing read yet
      .filter(pl.col("order_date") >= pl.date(2026, 1, 1))
      .with_columns(
          (pl.col("qty") * pl.col("unit_price") * (1 - pl.col("discount").fill_null(0))).alias("net"),
          pl.col("order_date").dt.truncate("1mo").alias("order_month"),
      )
      .group_by("order_month", "item_group")
      .agg(pl.col("net").sum().alias("revenue"), pl.col("order_id").n_unique().alias("orders"))
      .sort("order_month")
)
print(lf.explain())          # optimised plan: predicate & projection pushdown
monthly = lf.collect()       # engine="streaming" for larger-than-memory (verify API)

# Window functions with .over()
df = df.with_columns(
    pl.col("qty").cum_sum().over("customer_id", order_by="order_date").alias("running_qty"),
    pl.col("revenue").rank("dense", descending=True).over("item_group").alias("rank_in_group"),
)

# Read/write Delta (via deltalake package)
df = pl.read_delta("abfss://ws@onelake.dfs.fabric.microsoft.com/lh.Lakehouse/Tables/dim_item")
monthly.write_delta("out/monthly", mode="overwrite")
```

Polars vs pandas mental model: expressions (`pl.col(...)`) composed in contexts (`select`, `with_columns`, `filter`, `group_by().agg`), no index, strict types, lazy by default with `scan_*`.

---

## 4. DuckDB from Python

```python
import duckdb
con = duckdb.connect()                     # in-memory, or duckdb.connect("local.duckdb")
con.sql("INSTALL delta; LOAD delta;")
res = con.sql("""
    SELECT item_group, SUM(net_amount) AS revenue
    FROM read_parquet('lake/silver/sales_lines/*.parquet')
    WHERE order_date >= DATE '2026-01-01'
    GROUP BY ALL ORDER BY revenue DESC
""").pl()                                  # .df() for pandas, .arrow() for Arrow

# Query an in-memory pandas/Polars frame by variable name
top = con.sql("SELECT * FROM monthly WHERE revenue > 1e6").df()
```

Use cases: unit tests of SQL logic in CI (fast, no cloud), local prototyping of dbt models (dbt-duckdb), ad-hoc analysis of exports, lightweight pipelines on a single VM/Function.

---

## 5. PySpark essentials

```python
from pyspark.sql import functions as F, Window as W

lines = spark.read.table("silver.sales_lines")       # Fabric/Databricks lakehouse table
items = spark.read.table("silver.item")

w = W.partitionBy("customer_id").orderBy("order_date").rowsBetween(W.unboundedPreceding, 0)

result = (
    lines.filter(F.col("order_date") >= "2026-01-01")
         .join(F.broadcast(items.select("item_no", "item_group")), "item_no", "left")
         .withColumn("net", F.col("qty") * F.col("unit_price"))
         .withColumn("running_net", F.sum("net").over(w))
)

# Idempotent partition overwrite into Delta
(result.write.format("delta")
       .mode("overwrite")
       .option("replaceWhere", "order_date >= '2026-10-01' AND order_date < '2026-11-01'")
       .saveAsTable("gold.fct_sales_running"))

# MERGE (upsert) with Delta API
from delta.tables import DeltaTable
tgt = DeltaTable.forName(spark, "silver.customer")
(tgt.alias("t").merge(updates.alias("s"), "t.customer_no = s.customer_no")
    .whenMatchedUpdateAll(condition="s.modified_at > t.modified_at")
    .whenNotMatchedInsertAll()
    .execute())
```

Spark performance checklist:
- Avoid Python UDFs (row-at-a-time serialisation); use built-in functions or pandas UDFs (vectorised, Arrow).
- **Broadcast** small dimension tables in joins.
- Watch **shuffles** (joins, groupBy) and **skew** (one key dominates: salting, AQE skew join).
- Adaptive Query Execution (AQE) on by default in Spark 3+.
- Right-size partitions (~128–256 MB); `coalesce` before writing to avoid small files; or enable optimised writes.
- Cache only reused, expensive intermediate frames; unpersist.
- Don't `collect()`/`toPandas()` big data to the driver.
- Fabric: Native Execution Engine and V-Order settings; Databricks: Photon.

---

## 6. SQLAlchemy and database access

```python
from sqlalchemy import create_engine, text
import pandas as pd

# Azure SQL / Fabric Warehouse via ODBC with Entra ID (no passwords in code)
engine = create_engine(
    "mssql+pyodbc://@myserver.database.windows.net/mydb"
    "?driver=ODBC+Driver+18+for+SQL+Server&Authentication=ActiveDirectoryMsi",
    fast_executemany=True,
)

with engine.begin() as conn:                         # transaction; commits on success
    rows = conn.execute(
        text("SELECT item_no, description FROM dbo.item WHERE item_group = :g"),
        {"g": "SPARE"},                              # bound parameters — never f-strings (SQL injection)
    ).mappings().all()

df = pd.read_sql(text("SELECT * FROM dbo.v_open_orders"), engine)
df.to_sql("stage_open_orders", engine, schema="stg", if_exists="replace", index=False, chunksize=10_000)
```

Notes: for bulk loads prefer native bulk paths (COPY INTO, BCP, `fast_executemany`, Spark connectors, Parquet + `COPY`/`OPENROWSET`) over row inserts. Use connection pooling, timeouts, retries on transient errors (Azure SQL error codes 40613, 40501, etc.).

---

## 7. pydantic for validation and contracts

```python
from datetime import date
from decimal import Decimal
from typing import Literal
from pydantic import BaseModel, Field, field_validator, ConfigDict

class SalesOrderLine(BaseModel):
    model_config = ConfigDict(extra="forbid", str_strip_whitespace=True)

    order_no: str = Field(pattern=r"^SO\d{7}$")
    line_no: int = Field(gt=0)
    item_no: str
    quantity: Decimal = Field(gt=0, max_digits=18, decimal_places=3)
    unit_price_eur: Decimal = Field(ge=0)
    requested_date: date
    channel: Literal["webshop", "edi", "sales_rep"]

    @field_validator("requested_date")
    @classmethod
    def not_in_past_far(cls, v: date) -> date:
        if v.year < 2000:
            raise ValueError("implausible requested_date")
        return v

# Validate API payloads; collect errors instead of failing whole batch
good, bad = [], []
for rec in api_records:
    try:
        good.append(SalesOrderLine.model_validate(rec))
    except Exception as e:                   # pydantic.ValidationError
        bad.append({"record": rec, "error": str(e)})   # route to quarantine table

print(SalesOrderLine.model_json_schema())    # JSON Schema → LLM structured output, docs, contracts
```

pydantic is also the backbone of LLM structured output (OpenAI/Anthropic SDKs, Instructor, PydanticAI) — see [llm-fundamentals-prompting.md](llm-fundamentals-prompting.md).

---

## 8. Notebooks vs pipelines

| | Notebooks (Jupyter, Fabric, Databricks) | Pipelines / packaged code |
|---|---|---|
| Strength | Exploration, visual feedback, documentation of analysis, quick prototypes | Reproducibility, testing, code review, reuse |
| Weakness | Hidden state (out-of-order execution), hard diffs, copy-paste reuse, weak testing | Slower iteration |
| Production use | OK on Fabric/Databricks *if*: parameterised, idempotent, logic in imported modules/libraries, version-controlled, run by orchestrator, tested | Default |

Path from notebook to production:
1. Restart & run-all must succeed (no hidden state).
2. Extract functions into a Python package (`src/` layout) with type hints.
3. Unit tests with pytest (small fixture frames; DuckDB/Polars locally).
4. Parameterise (dates, environment) — no hard-coded paths/credentials.
5. Logging (structured), error handling, retries for IO.
6. Package as wheel / Fabric environment library; notebook becomes a thin entry point.
7. CI: lint (ruff), type check (mypy/pyright), tests; CD to environments.
8. Orchestrate (Fabric pipeline / Airflow / Dagster), monitor.

Tools: `uv` (fast package/env manager), `ruff` (lint+format), `pytest`, `pre-commit`, `nbstripout` (strip outputs before commit), `jupytext` (pair notebooks with .py), Papermill (parameterised notebook runs).

Project layout:

```
data-pipelines/
  pyproject.toml
  src/pipelines/
    __init__.py
    extract/erp_api.py
    transform/sales.py
    validate/schemas.py
    utils/logging.py
  tests/
    test_sales.py
  notebooks/
    exploration/
    entrypoints/run_sales.ipynb   # thin: import + call
```

pytest example:

```python
import polars as pl
from pipelines.transform.sales import add_net_amount

def test_add_net_amount_handles_null_discount():
    df = pl.DataFrame({"qty": [2, 1], "unit_price": [10.0, 5.0], "discount": [None, 0.1]})
    out = add_net_amount(df)
    assert out["net"].to_list() == [20.0, 4.5]
```

---

## 9. Calling APIs robustly (ERP/SaaS ingestion)

```python
import httpx, time, random

def get_with_retry(client: httpx.Client, url: str, params: dict, max_tries: int = 5) -> dict:
    for attempt in range(1, max_tries + 1):
        r = client.get(url, params=params, timeout=30)
        if r.status_code == 429 or r.status_code >= 500:
            wait = float(r.headers.get("Retry-After", 2 ** attempt)) + random.random()
            time.sleep(wait)
            continue
        r.raise_for_status()
        return r.json()
    raise RuntimeError(f"Failed after {max_tries} tries: {url}")

def fetch_all(client, url):
    params, page = {"$top": 1000}, 0
    while url:
        data = get_with_retry(client, url, params if page == 0 else {})
        yield from data["value"]
        url = data.get("@odata.nextLink")      # OData paging (Dynamics/Business Central)
        page += 1
```

Write raw JSON responses to bronze (one file per page/batch with run id) before parsing — enables replay.

## Common mistakes

- Pulling whole tables into pandas to do a `GROUP BY` the warehouse could do.
- Spark for 50 MB of data (startup and cost overhead) — use DuckDB/Polars.
- `toPandas()` on large Spark frames → driver OOM.
- Floats for money; naive datetimes mixing timezones.
- Notebook-only production logic with copy-pasted cells across notebooks.
- f-string SQL with user input (injection).
- Credentials in notebooks; use Key Vault / managed identity.
- No validation of external API payloads → schema drift breaks silver silently.

## References

- pandas: https://pandas.pydata.org/
- Polars: https://pola.rs/ · User guide: https://docs.pola.rs/
- DuckDB Python API: https://duckdb.org/docs/
- Apache Spark (PySpark docs): https://spark.apache.org/docs/latest/api/python/
- Delta Lake Python (delta-rs): https://delta-io.github.io/delta-rs/
- Apache Arrow: https://arrow.apache.org/
- SQLAlchemy: https://www.sqlalchemy.org/
- pydantic: https://docs.pydantic.dev/
- pandera: https://pandera.readthedocs.io/
- pytest: https://docs.pytest.org/ · Ruff: https://docs.astral.sh/ruff/ · uv: https://docs.astral.sh/uv/
- Fabric Data Engineering: https://learn.microsoft.com/fabric/data-engineering/
- Sibling: [sql-patterns.md](sql-patterns.md), [modern-data-stack.md](modern-data-stack.md); [Engineering practices](../08-engineering/README.md)
