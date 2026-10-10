# Resources: Books, Courses, Documentation & Communities

> **TL;DR** — A curated reading and reference list for data architecture, analytics engineering, BI, ML, and LLM/agent engineering. Start with the "core shelf" (Kimball Toolkit, Fundamentals of Data Engineering, DDIA, AI Engineering), keep official docs bookmarked (dbt, Fabric, Power BI, Delta/Iceberg, Anthropic/OpenAI/Azure AI), and follow a few high-signal blogs. AI resources age fastest — check publication dates (as of 2026, verify).

---

## 1. Core shelf (read first)

| Book | Author(s) | Why read it | Link |
|---|---|---|---|
| The Data Warehouse Toolkit (3rd ed.) | Ralph Kimball, Margy Ross | Definitive dimensional modelling reference: 4-step process, SCDs, bus matrix, industry case studies | https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/books/data-warehouse-dw-toolkit/ |
| Fundamentals of Data Engineering | Joe Reis, Matt Housley | Vendor-neutral data engineering lifecycle and undercurrents; architecture trade-offs | https://www.oreilly.com/library/view/fundamentals-of-data/9781098108298/ |
| Designing Data-Intensive Applications (2nd ed. in progress/2025–26) | Martin Kleppmann (+ Chris Riccomini for 2nd ed.) | Storage engines, replication, partitioning, transactions, streams — the theory behind every data platform | https://dataintensive.net/ |
| AI Engineering | Chip Huyen | Building applications on foundation models: evaluation, prompting, RAG, agents, fine-tuning, inference optimisation | https://huyenchip.com/ |
| Data Mesh | Zhamak Dehghani | Original, full treatment of the four principles | https://www.oreilly.com/library/view/data-mesh/9781492092384/ |

## 2. Data modelling & warehousing

| Resource | Notes |
|---|---|
| Kimball Group — Dimensional Modeling Techniques (free articles) | https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/ |
| The Kimball Group Reader | Collected design tips |
| Agile Data Warehouse Design — Lawrence Corr | BEAM✲ method for modelstorming with business users |
| Building a Scalable Data Warehouse with Data Vault 2.0 — Dan Linstedt, Michael Olschimke | Data Vault reference |
| Building the Data Warehouse — W. H. Inmon | The Inmon/CIF perspective |
| Star Schema: The Complete Reference — Christopher Adamson | Deep star-schema patterns |
| Data Modeling Made Simple — Steve Hoberman | Conceptual/logical/physical modelling basics |
| Analytics Engineering with SQL and dbt — Rui Machado, Hélder Russa | dbt-centred modelling |
| Data Vault Alliance | https://datavaultalliance.com/ |

## 3. Data engineering, platforms & formats

| Resource | Link |
|---|---|
| dbt documentation & best-practice guides | https://docs.getdbt.com/ |
| dbt — How we structure our dbt projects | https://docs.getdbt.com/best-practices/how-we-structure/1-guide-overview |
| Microsoft Fabric documentation | https://learn.microsoft.com/fabric/ |
| Microsoft Learn training (Fabric, DP-600 / DP-700 certification paths) | https://learn.microsoft.com/training/ |
| Azure Architecture Center | https://learn.microsoft.com/azure/architecture/ |
| Delta Lake | https://delta.io/ |
| Apache Iceberg | https://iceberg.apache.org/ |
| Apache Hudi | https://hudi.apache.org/ |
| Apache Parquet | https://parquet.apache.org/ |
| Apache Spark | https://spark.apache.org/ |
| Spark: The Definitive Guide — Bill Chambers, Matei Zaharia | Spark fundamentals |
| Delta Lake: The Definitive Guide — Denny Lee et al. | Delta internals |
| Apache Iceberg: The Definitive Guide — Tomer Shiran et al. | Iceberg internals |
| DuckDB | https://duckdb.org/ |
| Polars | https://pola.rs/ |
| Apache Airflow / Dagster / Prefect | https://airflow.apache.org/ · https://dagster.io/ · https://www.prefect.io/ |
| Debezium | https://debezium.io/ |
| Streaming Systems — Tyler Akidau, Slava Chernyak, Reuven Lax | Watermarks, windows, exactly-once |
| Jay Kreps — The Log: What every software engineer should know about real-time data's unifying abstraction | https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying |

## 4. SQL

| Resource | Link |
|---|---|
| Use The Index, Luke — Markus Winand | https://use-the-index-luke.com/ |
| Modern SQL — Markus Winand | https://modern-sql.com/ |
| SQL Performance Explained — Markus Winand | Book version of the above |
| T-SQL Window Functions — Itzik Ben-Gan | Window functions deep dive (SQL Server) |
| PostgreSQL documentation | https://www.postgresql.org/docs/ |
| SQLFluff (linter) | https://sqlfluff.com/ |

## 5. Power BI & DAX

| Resource | Link |
|---|---|
| Power BI documentation & guidance | https://learn.microsoft.com/power-bi/ |
| Star schema guidance | https://learn.microsoft.com/power-bi/guidance/star-schema |
| DAX reference | https://learn.microsoft.com/dax/ |
| SQLBI (Marco Russo, Alberto Ferrari) — articles, DAX Guide, videos | https://www.sqlbi.com/ · https://dax.guide/ |
| The Definitive Guide to DAX — Russo & Ferrari | Essential DAX book |
| Tabular Editor / DAX Studio | https://tabulareditor.com/ · https://daxstudio.org/ |
| Guy in a Cube (YouTube channel) | Practical Power BI/Fabric videos |

## 6. Governance, quality & privacy

| Resource | Link |
|---|---|
| DAMA-DMBOK (2nd ed., revised) | https://www.dama.org/cpages/body-of-knowledge |
| Data Governance: The Definitive Guide — Evren Eryurek et al. | Governance in cloud platforms |
| Data Quality Fundamentals — Barr Moses, Lior Gavish, Molly Vorwerck | Data observability and reliability |
| Driving Data Quality with Data Contracts — Andrew Jones | Data contracts in practice |
| Open Data Contract Standard (Bitol) | https://github.com/bitol-io/open-data-contract-standard |
| Microsoft Purview | https://learn.microsoft.com/purview/ |
| OpenLineage / DataHub | https://openlineage.io/ · https://datahubproject.io/ |
| Great Expectations / Soda | https://greatexpectations.io/ · https://www.soda.io/ |
| GDPR text (EUR-Lex) / article browser | https://eur-lex.europa.eu/eli/reg/2016/679/oj · https://gdpr-info.eu/ |
| European Data Protection Board guidelines | https://www.edpb.europa.eu/ |
| EU AI Act text / explorer | https://eur-lex.europa.eu/eli/reg/2024/1689/oj · https://artificialintelligenceact.eu/ |

## 7. Analytics, BI & decision-making

| Resource | Notes |
|---|---|
| Storytelling with Data — Cole Nussbaumer Knaflic | Chart and narrative design; https://www.storytellingwithdata.com/ |
| The Visual Display of Quantitative Information — Edward Tufte | Classic visual principles; https://www.edwardtufte.com/ |
| Information Dashboard Design — Stephen Few | Dashboard-specific guidance |
| The Big Book of Dashboards — Wexler, Shaffer, Cotgreave | Patterns by use case |
| IBCS standards | https://www.ibcs.com/ |
| How to Measure Anything — Douglas Hubbard | Measuring "intangibles", value of information |
| Thinking in Bets — Annie Duke | Decision quality vs outcome quality |
| Lean Analytics — Croll & Yoskovitz | Metrics by business model/stage |
| Fabric adoption roadmap | https://learn.microsoft.com/power-bi/guidance/fabric-adoption-roadmap |

## 8. Machine learning & forecasting

| Resource | Link / notes |
|---|---|
| Hands-On Machine Learning with Scikit-Learn, Keras & TensorFlow / PyTorch — Aurélien Géron | Practical ML |
| An Introduction to Statistical Learning (free PDF, R and Python editions) | https://www.statlearning.com/ |
| Forecasting: Principles and Practice (3rd ed., free) — Hyndman & Athanasopoulos | https://otexts.com/fpp3/ |
| Designing Machine Learning Systems — Chip Huyen | ML in production, MLOps |
| Reliable Machine Learning — Cathy Chen et al. | SRE for ML |
| Google — Rules of Machine Learning | https://developers.google.com/machine-learning/guides/rules-of-ml |
| Google Cloud — MLOps maturity levels | https://cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning |
| Microsoft — MLOps maturity model | https://learn.microsoft.com/azure/architecture/ai-ml/guide/mlops-maturity-model |
| scikit-learn user guide | https://scikit-learn.org/stable/user_guide.html |
| MLflow | https://mlflow.org/ |
| Interpretable Machine Learning — Christoph Molnar (free online) | https://christophm.github.io/interpretable-ml-book/ |

## 9. LLMs, RAG & agents (fast-moving — as of 2026, verify)

| Resource | Link |
|---|---|
| Anthropic — Building effective agents | https://www.anthropic.com/research/building-effective-agents |
| Anthropic engineering blog | https://www.anthropic.com/engineering |
| Anthropic — Contextual Retrieval | https://www.anthropic.com/news/contextual-retrieval |
| Anthropic documentation (prompting, tool use, caching) | https://docs.anthropic.com/ |
| Model Context Protocol | https://modelcontextprotocol.io/ |
| OpenAI platform docs & cookbook | https://platform.openai.com/docs · https://cookbook.openai.com/ |
| Azure AI Foundry documentation | https://learn.microsoft.com/azure/ai-foundry/ |
| Azure AI Search | https://learn.microsoft.com/azure/search/ |
| Azure AI Document Intelligence | https://learn.microsoft.com/azure/ai-services/document-intelligence/ |
| Microsoft GraphRAG | https://microsoft.github.io/graphrag/ |
| RAGAS | https://docs.ragas.io/ |
| Prompt Engineering Guide (DAIR.AI) | https://www.promptingguide.ai/ |
| Hugging Face course & docs | https://huggingface.co/learn |
| Build a Large Language Model (From Scratch) — Sebastian Raschka | Understand transformers by building one |
| Hands-On Large Language Models — Jay Alammar, Maarten Grootendorst | Visual, practical intro |
| Jay Alammar — The Illustrated Transformer | https://jalammar.github.io/illustrated-transformer/ |
| Andrej Karpathy — Neural Networks: Zero to Hero (videos) | https://karpathy.ai/ |
| Chip Huyen's blog | https://huyenchip.com/blog/ |
| Simon Willison's weblog (LLM tooling, prompt injection) | https://simonwillison.net/ |
| Hamel Husain — writing on LLM evals | https://hamel.dev/ |
| Eugene Yan — applied ML/LLM patterns | https://eugeneyan.com/ |

Key papers:
- Attention Is All You Need — https://arxiv.org/abs/1706.03762
- RAG (Lewis et al.) — https://arxiv.org/abs/2005.11401
- Chain-of-Thought — https://arxiv.org/abs/2201.11903
- ReAct — https://arxiv.org/abs/2210.03629
- LoRA — https://arxiv.org/abs/2106.09685
- Lost in the Middle — https://arxiv.org/abs/2307.03172
- RAGAS — https://arxiv.org/abs/2309.15217
- GraphRAG — https://arxiv.org/abs/2404.16130
- Judging LLM-as-a-Judge — https://arxiv.org/abs/2306.05685

## 10. AI security & responsible AI

| Resource | Link |
|---|---|
| OWASP Top 10 for LLM Applications / GenAI Security Project | https://owasp.org/www-project-top-10-for-large-language-model-applications/ · https://genai.owasp.org/ |
| NIST AI Risk Management Framework | https://www.nist.gov/itl/ai-risk-management-framework |
| ISO/IEC 42001 | https://www.iso.org/standard/81230.html |
| Microsoft Responsible AI | https://www.microsoft.com/ai/responsible-ai |
| MITRE ATLAS | https://atlas.mitre.org/ |
| Azure AI Content Safety | https://learn.microsoft.com/azure/ai-services/content-safety/ |

## 11. Newsletters, blogs, communities

| Resource | Focus | Link |
|---|---|---|
| Martin Fowler's site (data mesh, architecture) | Architecture | https://martinfowler.com/ |
| dbt Community & blog | Analytics engineering | https://www.getdbt.com/community |
| Data Engineering Weekly | News digest | https://www.dataengineeringweekly.com/ |
| Benn Stancil's newsletter (benn.substack) | Data industry commentary | https://benn.substack.com/ |
| Microsoft Fabric blog | Product updates | https://blog.fabric.microsoft.com/ |
| Power BI blog | Product updates | https://powerbi.microsoft.com/blog/ |
| SQLBI | DAX & modelling | https://www.sqlbi.com/articles/ |
| Seattle Data Guy / Data Engineering subreddit | Community | https://www.reddit.com/r/dataengineering/ |
| Latent Space (podcast/newsletter) | AI engineering | https://www.latent.space/ |

## 12. Suggested learning paths

| Goal | Path |
|---|---|
| Solid dimensional modelling | Kimball Toolkit ch. 1–8 → Kimball techniques site → model one business process end-to-end with bus matrix → [dimensional-modeling.md](dimensional-modeling.md) |
| Fabric/Power BI platform lead | Fabric docs + DP-600/DP-700 learning paths → SQLBI DAX → Fabric adoption roadmap → [microsoft-fabric-power-bi.md](microsoft-fabric-power-bi.md) |
| Analytics engineering | dbt Fundamentals course → structure guide → tests/contracts → [modern-data-stack.md](modern-data-stack.md) |
| Applied ML for operations | ISLP → FPP3 (forecasting) → Designing ML Systems → [ml-fundamentals-mlops.md](ml-fundamentals-mlops.md) |
| LLM applications | AI Engineering (Huyen) → Anthropic "Building effective agents" → build a RAG with eval set → OWASP LLM Top 10 → [rag-architecture.md](rag-architecture.md), [ai-agents-automation.md](ai-agents-automation.md) |
| Governance | DMBOK overview → Data Quality Fundamentals → GDPR + AI Act summaries → [data-quality-governance.md](data-quality-governance.md) |

## References

- See links inline above. Related section indexes: [Software architecture](../05-software-architecture/README.md), [Cloud architecture](../06-cloud-architecture/README.md), [Engineering](../08-engineering/README.md), [Enterprise architecture & business](../11-enterprise-architecture-business/README.md), [Laws, principles & mental models](../10-laws-principles-mental-models/README.md).
- Section index: [README.md](README.md)
