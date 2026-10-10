# Azure Storage, Databases & Key Vault

> **TL;DR** — Storage accounts give Blob/Files/Queues/Tables with **access tiers** (hot/cool/cold/archive) and **redundancy** (LRS/ZRS/GRS/GZRS, RA- for read access to secondary); use lifecycle policies and ZRS/GZRS for production. Relational default: **Azure SQL Database** (vCore, serverless for spiky dev/low-use, Hyperscale for large/fast-scaling) or **Azure Database for PostgreSQL Flexible Server**; **SQL Managed Instance** for near-100% SQL Server compatibility. **Cosmos DB** for globally distributed / high-scale NoSQL — design the **partition key** first, understand **RU/s** and the **5 consistency levels**. Secrets, keys and certificates go in **Key Vault** with RBAC, soft delete and purge protection.

---

## 1. Storage accounts

### 1.1 Services

| Service | Use | Notes |
|---|---|---|
| **Blob** | Objects: files, backups, data lake (with **hierarchical namespace** = ADLS Gen2), static websites | Block, append, page blobs |
| **Azure Files** | SMB/NFS file shares | Replace file servers; Entra Kerberos / AD DS auth; Azure File Sync for on-prem caching |
| **Queue Storage** | Simple queues (64 KB messages) | Cheap; no ordering/sessions/topics |
| **Table Storage** | Key-value NoSQL | Cheap; Cosmos DB for Table API for more |
| **Managed Disks** | VM disks | Separate resource type, not in storage account |

Account kinds: **Standard general-purpose v2** (default), **Premium** block blob / file shares / page blobs (SSD, low latency).

### 1.2 Blob access tiers

| Tier | Storage cost | Access cost | Min retention (early deletion charge) | Latency | Use |
|---|---|---|---|---|---|
| **Hot** | Highest | Lowest | None | ms | Active data |
| **Cool** | Lower | Higher | 30 days | ms | Infrequent access, short-term backup |
| **Cold** | Lower still | Higher | 90 days | ms | Rarely accessed but needs online access |
| **Archive** | Lowest | Highest + **rehydration** (hours; standard up to 15 h, high priority < 1 h for small objects) | 180 days | Hours | Long-term retention, compliance |

Tier can be set per blob; default account tier hot/cool. Archive is offline — not readable until rehydrated. *Verify current prices & retention minimums.*

**Lifecycle management policy** (Bicep/JSON):

```json
{
  "rules": [{
    "name": "logs-tiering",
    "enabled": true,
    "type": "Lifecycle",
    "definition": {
      "filters": { "blobTypes": ["blockBlob"], "prefixMatch": ["logs/"] },
      "actions": {
        "baseBlob": {
          "tierToCool":    { "daysAfterModificationGreaterThan": 30 },
          "tierToCold":    { "daysAfterModificationGreaterThan": 90 },
          "tierToArchive": { "daysAfterModificationGreaterThan": 180 },
          "delete":        { "daysAfterModificationGreaterThan": 2555 }
        },
        "snapshot": { "delete": { "daysAfterCreationGreaterThan": 90 } }
      }
    }
  }]
}
```

### 1.3 Redundancy options

| Option | Copies | Where | Survives | Durability (≥, per year) | Read from secondary |
|---|---|---|---|---|---|
| **LRS** | 3 | One datacenter | Disk/rack failure | 11 nines | — |
| **ZRS** | 3 | 3 zones in primary region | Zone failure | 12 nines | — |
| **GRS** | 6 | LRS primary + LRS secondary (paired region) | Region failure (after failover) | 16 nines | No |
| **RA-GRS** | 6 | Same as GRS | Region failure | 16 nines | **Yes** (read-only `-secondary` endpoint) |
| **GZRS** | 6 | ZRS primary + LRS secondary | Zone + region | 16 nines | No |
| **RA-GZRS** | 6 | Same as GZRS | Zone + region | 16 nines | **Yes** |

Notes:
- Geo-replication is **asynchronous** — RPO typically < 15 min (no SLA guarantee for standard; Geo priority replication offers SLA for blob — *verify*).
- Failover: **customer-managed failover** (unplanned or planned) changes DNS; after failover, account becomes LRS until reconfigured.
- Not all services support all options (e.g. Premium = LRS/ZRS only; Archive tier not on ZRS/GZRS accounts — *verify*).
- **Object replication** (async blob copy between accounts) for custom DR/geo-distribution.

### 1.4 Data protection

| Feature | Protects against |
|---|---|
| **Soft delete** (blobs, containers, file shares) | Accidental delete |
| **Versioning** | Overwrites |
| **Point-in-time restore** (block blobs) | Bulk corruption |
| **Immutable storage** (WORM: time-based retention, legal hold; version-level) | Ransomware, compliance (SEC 17a-4 style) |
| **Resource lock** `CanNotDelete` | Accidental account deletion |
| **Azure Backup** (blobs operational/vaulted, Files) | Logical corruption, separate trust boundary |

### 1.5 Storage security checklist

- [ ] `allowBlobPublicAccess: false`
- [ ] `allowSharedKeyAccess: false` where clients support Entra auth (data-plane RBAC)
- [ ] `minimumTlsVersion: TLS1_2`, `supportsHttpsTrafficOnly: true`
- [ ] `publicNetworkAccess: Disabled` + private endpoints (per sub-resource: blob, file, dfs, queue, table)
- [ ] Defender for Storage (activity monitoring + malware scanning on upload)
- [ ] CMK if required by policy (see security file)
- [ ] User-delegation SAS only, short-lived
- [ ] Diagnostic logs to Log Analytics

---

## 2. Relational databases

### 2.1 Azure SQL family

| Option | Model | Compatibility | Use |
|---|---|---|---|
| **Azure SQL Database** (single / elastic pool) | PaaS DB | Database-scoped (no SQL Agent, no cross-DB queries except elastic query; no CLR assemblies from files) | New apps, SaaS, most LOB |
| **Azure SQL Managed Instance** | PaaS instance | Near 100% SQL Server (SQL Agent, cross-DB, Service Broker, CLR, linked servers, DB Mail) | Lift-and-shift of SQL Server estates; VNet-native |
| **SQL Server on Azure VM** | IaaS | 100% | OS access, SSRS/SSIS on same box, unsupported features, specific versions |
| **SQL database in Microsoft Fabric** | SaaS | SQL Database engine | Analytics-adjacent operational DBs — *verify GA status* |

### 2.2 Azure SQL Database purchasing models & tiers

| Model / tier | How | Storage | HA | Best for |
|---|---|---|---|---|
| **DTU** Basic / Standard / Premium | Bundled compute+IO+memory units | Up to 1 TB (Premium up to 4 TB) | Basic/Std: remote storage; Premium: local SSD + AlwaysOn replicas | Small simple DBs, legacy; less transparent |
| **vCore – General Purpose** (provisioned) | Choose vCores + hardware gen | Up to 4 TB, remote premium storage | Single compute + remote storage; zone redundancy option | Most business workloads |
| **vCore – General Purpose serverless** | Auto-scale between min–max vCores, **auto-pause** after idle | Per GB | Same as GP | Dev/test, intermittent, unpredictable usage. Auto-pause → resume latency (~1 min first connection) |
| **vCore – Business Critical** | Local SSD, 4-replica AlwaysOn AG, **free readable secondary** | Up to 4 TB | Highest, zone-redundant | Low latency, high IO, critical OLTP |
| **vCore – Hyperscale** | Distributed architecture: compute + page servers + log service | Up to 128 TB | Fast backups/restores (snapshot), named & HA replicas, rapid scale | Large DBs, fast scale-up/out reads; also good default now since pricing changes — *verify* |
| **Elastic pool** | Share eDTU/vCores among many DBs | — | — | SaaS DB-per-tenant with varying load |

Feature notes:
- **Azure Hybrid Benefit** applies to vCore (bring SQL Server licenses with SA) — big saving.
- **Reserved capacity** for vCore compute (1/3 yr).
- **Auto-failover groups**: geo-replication + listener endpoint for automatic region failover (RPO ≈ 5 s, RTO ~ 1 h with automatic policy grace period; *verify SLA*).
- **Active geo-replication**: up to 4 readable secondaries, manual failover.
- Backups: automated full/diff/log, PITR 1–35 days, **LTR** up to 10 years.
- Security: Entra-only authentication, TDE (service- or customer-managed key), Always Encrypted, Ledger, Defender for SQL, auditing to Log Analytics.

```bash
# Serverless GP DB, min 0.5 vCore, max 4, auto-pause after 60 min
az sql db create -g rg-app1-dev -s sql-app1-dev-weu-001 -n sqldb-app1 \
  --edition GeneralPurpose --compute-model Serverless --family Gen5 \
  --min-capacity 0.5 --capacity 4 --auto-pause-delay 60 --backup-storage-redundancy Local
```

### 2.3 Azure Database for PostgreSQL – Flexible Server

| Topic | Detail |
|---|---|
| Tiers | **Burstable** (B-series; dev/small), **General Purpose** (D-series), **Memory Optimized** (E-series) |
| HA | Zone-redundant HA (standby in another zone, sync replication) or same-zone HA; ~60–120 s failover |
| Read replicas | Up to 5 (same or cross-region), promote for DR; **virtual endpoints** |
| Networking | Public access w/ firewall, **private access (VNet injection, delegated subnet)**, or private endpoint |
| Auth | Entra ID authentication + PostgreSQL auth |
| Extensions | `pg_vector` (vector search), PostGIS, `pg_cron`, `pg_partman`, `azure_ai`... (allow-listed) |
| Ops | Stop/start (up to 7 days), scheduled maintenance windows, PgBouncer built-in, query store, intelligent tuning |
| Scale-out | **Elastic clusters** (Citus-based) for sharding — *verify GA*; older "Azure Cosmos DB for PostgreSQL" |
| Single Server | Retired (March 2025) → migrate to Flexible Server |

MySQL equivalent: **Azure Database for MySQL – Flexible Server**.

### 2.4 Choosing a relational option

| Requirement | Pick |
|---|---|
| New .NET LOB app, standard SQL | Azure SQL DB GP (serverless for dev) |
| Existing SQL Server w/ Agent jobs, cross-DB queries, CLR | SQL Managed Instance |
| Open-source stack, extensions, vector search | PostgreSQL Flexible Server |
| > 4 TB or rapid read scale-out | Hyperscale |
| Many small tenant DBs | Elastic pool |
| Need OS access / SSIS on box | SQL on VM |

---

## 3. Azure Cosmos DB

### 3.1 APIs

| API | Model | Notes |
|---|---|---|
| **NoSQL** (native) | JSON documents, SQL-like query | Richest features first; default choice |
| MongoDB (RU-based) / **MongoDB vCore** | Mongo wire protocol | vCore = cluster-based pricing, familiar Mongo semantics |
| Apache Cassandra | Wide column | Also Managed Instance for Apache Cassandra |
| Apache Gremlin | Graph | |
| Table | Key-value | Upgrade path from Table Storage |
| PostgreSQL | Distributed PostgreSQL (Citus) | Being repositioned — *verify* |

### 3.2 Resource model & RU

`Account → Database → Container → Items`, with **logical partitions** (by partition key value) mapped to **physical partitions** (each up to 10,000 RU/s and 50 GB).

**Request Units (RU)**: normalized cost of operations. 1 RU ≈ point read of a 1 KB item by id + partition key. Writes ≈ 5+ RU/KB; queries vary (check `x-ms-request-charge`).

| Throughput mode | How billed | Use |
|---|---|---|
| **Provisioned (manual)** | RU/s per hour (min 400 RU/s per container, or shared at DB level) | Steady, predictable load |
| **Autoscale** | Scales between 10%–100% of max RU/s; billed on highest RU/s per hour | Variable load; production default for many |
| **Serverless** | Per RU consumed + storage | Dev/test, low/intermittent traffic; single region only — *verify* |

Other: **free tier** (first 1000 RU/s + 25 GB per account, one per subscription), reserved capacity, **burst capacity**, **priority-based execution**, **throughput redistribution** across partitions.

### 3.3 Partition key design

Rules:
1. **High cardinality** (many distinct values) — avoid `country`, `status`, `type`.
2. **Even distribution of storage and requests** — avoid hot partitions (e.g. `date` for time-series writes all hit today).
3. **Used in most queries' filter** — so queries are single-partition (cross-partition fan-out costs more RU and latency).
4. Logical partition max **20 GB** — size of all items with one key value.
5. **Immutable** — cannot change PK of a container without migration (copy job).

Patterns:
- `/tenantId` for multi-tenant — beware large tenants → use **hierarchical partition keys** (`/tenantId`, `/userId`, `/sessionId`), up to 3 levels.
- `/id` for pure key-value point-reads (maximum distribution).
- **Synthetic keys**: `"pk": "deviceId-2026-10"` (concat) or suffix bucketing `deviceId-<0..9>` for write-heavy.
- Store items of different types in one container with shared PK (e.g. order + orderLines under `/customerId`) — model for access patterns, embed vs reference.

```json
// Point read: cheapest (≈1 RU for 1 KB)
GET /dbs/sales/colls/orders/docs/ord-123  (x-ms-documentdb-partitionkey: ["cust-42"])

// Single-partition query
SELECT * FROM c WHERE c.customerId = 'cust-42' AND c.type = 'order' ORDER BY c.createdAt DESC

// Cross-partition query (fan-out — avoid on hot paths)
SELECT * FROM c WHERE c.status = 'open'
```

### 3.4 Consistency levels (5)

From strongest to weakest:

| Level | Guarantee | Read cost | Latency / availability | Typical use |
|---|---|---|---|---|
| **Strong** | Linearizable: reads see the latest committed write | 2× RU (reads from quorum) | Highest latency; multi-region writes not supported; region distance limits | Financial ledgers, inventory where correctness > latency |
| **Bounded staleness** | Reads lag by at most K versions or T time; consistent prefix beyond | 2× RU | Lower than strong; good for single-write-region global | Global apps needing near-strong with bounded lag |
| **Session** (default) | Within a client session: read-your-writes, monotonic reads/writes, consistent prefix | 1× | Low | Most apps — user sees own updates |
| **Consistent prefix** | Never see out-of-order writes (may be stale) | 1× | Low | Social feeds, notifications |
| **Eventual** | No ordering guarantee; converges eventually | 1× | Lowest | Counters, likes, non-critical reads |

Set at account level; can relax (not strengthen) per request. Session consistency requires passing the **session token** across app instances if the session spans them.

### 3.5 Other Cosmos features

- **Global distribution**: add regions; **multi-region writes** (conflict resolution: last-writer-wins on `_ts` or custom); **service-managed failover**; 99.999% read/write SLA with multi-region writes (*verify*).
- **Change feed**: ordered per partition log of changes → Functions trigger, materialized views, event sourcing, CQRS.
- **Indexing policy**: all paths indexed by default — exclude unused paths to cut write RU; composite indexes for ORDER BY on multiple fields.
- **TTL** per item/container, **analytical store / Synapse Link** (or **Fabric mirroring**), **vector search** (DiskANN), full-text search.
- **Backups**: periodic (default) or **continuous** (PITR 7 or 30 days).
- Security: Entra RBAC for data plane (disable keys via `disableLocalAuth`), private endpoints, CMK.

### 3.6 When to use Cosmos DB / when not

| Use | Avoid |
|---|---|
| Global distribution, low-latency reads/writes worldwide | Small internal app with relational data and reporting → SQL/PostgreSQL |
| Massive scale, predictable single-digit ms point reads | Ad-hoc analytical queries / complex joins |
| Schema-flexible documents, IoT, catalogs, user profiles, chat history for AI apps | Team unfamiliar with partitioning, no access-pattern analysis |
| Event-sourcing store with change feed | Strict multi-document ACID across partitions (transactions only within a logical partition) |

---

## 4. Caching & other data services

| Service | Use | Notes |
|---|---|---|
| **Azure Cache for Redis** / **Azure Managed Redis** | Cache-aside, session store, rate limiter, pub/sub | Azure Managed Redis (Redis Enterprise-based) is the strategic offering; legacy Basic/Standard/Premium tiers retirement announced — *verify timeline* |
| **Azure AI Search** | Full-text + vector + hybrid search, RAG | See [Data & AI architecture](../07-data-ai-architecture/README.md) |
| **Azure Data Explorer / Fabric Eventhouse** | Time-series & log analytics (KQL) | |
| **Azure NetApp Files** | High-perf NFS/SMB | SAP, HPC, VDI |
| **Azure Data Lake Storage Gen2** | Blob + hierarchical namespace + POSIX ACLs | Data lake foundation |

---

## 5. Azure Key Vault

### 5.1 Objects & tiers

| Object | Examples | Notes |
|---|---|---|
| **Secrets** | Connection strings, API keys, passwords | Up to 25 KB; versions; expiry dates; Event Grid events on near-expiry |
| **Keys** | RSA/EC keys for CMK, signing, wrap/unwrap | Software (Standard) or **HSM-protected** (Premium) |
| **Certificates** | TLS certs w/ auto-renewal from integrated CAs (DigiCert, GlobalSign) or self-signed | App Service / App Gateway / Front Door can reference |

| Variant | Use |
|---|---|
| Key Vault **Standard** | Secrets, software keys |
| Key Vault **Premium** | HSM-backed keys (FIPS 140 Level 3 validated HSMs — *verify level*) |
| **Managed HSM** | Single-tenant HSM pool, full key sovereignty |
| **Azure Cloud HSM / Dedicated HSM** | Lift-and-shift HSM scenarios — *verify product status* |

### 5.2 Best practices

- [ ] **One vault per application per environment** (blast radius, access separation); vaults are free, operations billed
- [ ] **RBAC permission model** (not access policies): *Key Vault Secrets User* for apps, *Secrets Officer* for pipelines
- [ ] **Soft delete** (mandatory) + **purge protection** (required for CMK)
- [ ] Private endpoint, `publicNetworkAccess: Disabled` (allow trusted Microsoft services if needed)
- [ ] App access via **managed identity**; **Key Vault references** in App Service/Functions settings: `@Microsoft.KeyVault(SecretUri=https://kv-x.vault.azure.net/secrets/DbPassword/)`
- [ ] Prefer **no secret at all** (managed identity to SQL/Storage/Service Bus) over storing secrets
- [ ] Rotation: key rotation policy for keys; Event Grid `SecretNearExpiry` → Function rotates
- [ ] Diagnostic logs (AuditEvent) to Log Analytics; alert on unusual access
- [ ] Throttling limits exist — cache secrets in app, don't read per request

```bicep
resource kv 'Microsoft.KeyVault/vaults@2023-07-01' = {
  name: 'kv-invoice-prod-weu'
  location: location
  properties: {
    tenantId: subscription().tenantId
    sku: { family: 'A', name: 'standard' }
    enableRbacAuthorization: true
    enableSoftDelete: true
    softDeleteRetentionInDays: 90
    enablePurgeProtection: true
    publicNetworkAccess: 'Disabled'
    networkAcls: { defaultAction: 'Deny', bypass: 'AzureServices' }
  }
}
```

### 5.3 Azure App Configuration

Central feature flags + settings (External Configuration Store pattern); references Key Vault secrets; labels per environment; snapshots; private endpoint; use alongside Key Vault (config ≠ secrets).

---

## 6. Data store selection cheat sheet

| Data shape / need | Azure | GCP | AWS |
|---|---|---|---|
| Relational OLTP | Azure SQL / PostgreSQL Flexible | Cloud SQL / AlloyDB | RDS / Aurora |
| Global relational, strong consistency | (Cosmos DB strong single-write / SQL Hyperscale geo) | **Spanner** | Aurora DSQL / Aurora Global |
| Document | Cosmos DB NoSQL | Firestore | DynamoDB / DocumentDB |
| Key-value cache | Azure Managed Redis | Memorystore | ElastiCache / MemoryDB |
| Object | Blob Storage | Cloud Storage | S3 |
| File share | Azure Files / NetApp Files | Filestore | EFS / FSx |
| Warehouse / lakehouse | Fabric / Synapse / Databricks | BigQuery | Redshift / Databricks |
| Time series / logs | Data Explorer / Fabric RTI | BigQuery / Bigtable | Timestream / OpenSearch |
| Search / vector | AI Search, Cosmos DB vector, pgvector | Vertex AI Vector Search, AlloyDB | OpenSearch, Aurora pgvector |

---

## 7. Common mistakes

1. LRS for production data that must survive a zone outage.
2. Archive tier for data that needs ad-hoc access (rehydration hours + costs).
3. Low-cardinality or time-based Cosmos partition key → hot partitions, throttling (429).
4. Using Strong consistency "to be safe" globally → latency and 2× read cost.
5. Cross-partition queries on hot paths; default "index everything" on write-heavy containers.
6. Azure SQL DTU Basic in production; no Hybrid Benefit applied to vCore.
7. Serverless SQL auto-pause on workloads with frequent health checks (never pauses, or users wait on resume).
8. One Key Vault for everything; access policies granting `get/list` to many identities.
9. Storing secrets in App Configuration instead of Key Vault references.
10. No tested restore — backups that were never restored are hypotheses.

## References

- Azure Storage documentation — https://learn.microsoft.com/azure/storage/
- Blob access tiers — https://learn.microsoft.com/azure/storage/blobs/access-tiers-overview
- Storage redundancy — https://learn.microsoft.com/azure/storage/common/storage-redundancy
- Azure SQL documentation — https://learn.microsoft.com/azure/azure-sql/
- Azure Database for PostgreSQL – Flexible Server — https://learn.microsoft.com/azure/postgresql/flexible-server/
- Azure Cosmos DB — https://learn.microsoft.com/azure/cosmos-db/
- Cosmos DB consistency levels — https://learn.microsoft.com/azure/cosmos-db/consistency-levels
- Cosmos DB partitioning — https://learn.microsoft.com/azure/cosmos-db/partitioning-overview
- Cosmos DB request units — https://learn.microsoft.com/azure/cosmos-db/request-units
- Azure Key Vault — https://learn.microsoft.com/azure/key-vault/
- Azure App Configuration — https://learn.microsoft.com/azure/azure-app-configuration/
- Data store decision guidance — https://learn.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview
- Wikipedia: Consistency model — https://en.wikipedia.org/wiki/Consistency_model
- Related: [Data & AI architecture](../07-data-ai-architecture/README.md), [reliability-observability.md](reliability-observability.md)
