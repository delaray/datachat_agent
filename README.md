<div align="center">

# 📊 DataChat Agent — Retail Analysis Assistant

**Ask questions about retail sales in plain English, get answers you can trust, and keep your findings as reports.**

![Python](https://img.shields.io/badge/Python-3.13-3776AB?logo=python&logoColor=white)
![LangGraph](https://img.shields.io/badge/LangGraph-agent-1C3C3C?logo=langchain&logoColor=white)
![Gemini](https://img.shields.io/badge/Google_Gemini-LLM-8E75B2?logo=googlegemini&logoColor=white)
![BigQuery](https://img.shields.io/badge/BigQuery-read--only-669DF6?logo=googlebigquery&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-18-4169E1?logo=postgresql&logoColor=white)
[![Docker](https://img.shields.io/badge/Docker-delaray%2Fdatachat%3A1.0.1-2496ED?logo=docker&logoColor=white)](https://hub.docker.com/r/delaray/datachat)

[Install](docs/user-guide/01-installation.md) ·
[Use](docs/user-guide/02-using-datachat.md) ·
[Roles & Security](docs/user-guide/03-roles-and-security.md) ·
[Examples](docs/user-guide/04-examples.md) ·
[Troubleshooting](docs/user-guide/05-troubleshooting.md)

</div>

---

## 🧭 What is DataChat?

DataChat is a **terminal chat assistant for retail executives**. You type a question such as
*"Show item counts by category"*, and DataChat:

1. checks that you are allowed to see the data you asked about,
2. asks Google **Gemini** to draft a query, then checks that query against strict rules,
3. runs it against the retail dataset in Google **BigQuery** (read-only),
4. shows the answer as a table, with notes on how to read it.

You can refine the question in follow-up turns ("*completed items only*", "*by month*"), turn your
analyses into a **saved report** with findings and action items, and later **delete** reports you
own. Every deletion needs your explicit confirmation.

```mermaid
flowchart LR
    you(["👤 You<br/>(terminal)"]) -->|question| dc["🤖 DataChat<br/>LangGraph agent"]
    dc -->|draft SQL| gem["✨ Gemini"]
    dc -->|validated, scoped query| bq[("📦 BigQuery<br/>retail data · read-only")]
    dc <-->|your reports| pg[("🗄️ PostgreSQL<br/>report library")]
    dc -->|checked answer| you

    classDef user fill:#FFF4CE,stroke:#C9A100,color:#3D3000
    classDef agent fill:#DCEBFF,stroke:#2F6FD6,color:#0B2A5B
    classDef ext fill:#EDE4FF,stroke:#7A5AC8,color:#2E1A66
    classDef store fill:#DFF5E3,stroke:#2E9B4A,color:#0E3B1B
    class you user
    class dc agent
    class gem ext
    class bq,pg store
```

*How to read this diagram:* your question goes only to DataChat. DataChat uses Gemini to draft the
query, but **trusted code** checks that draft and adds your product restrictions before BigQuery runs
it. Reports are stored in PostgreSQL, and each executive can see only their own.

## ✅ What you can do

| | Task | You type… | Details |
|:-:|---|---|---|
| 🔍 | **Analyze data** over several turns | `Show item counts by category` → `completed items only` → `by month` | [Using DataChat § 1](docs/user-guide/02-using-datachat.md#task-1--analyze-data) |
| 📝 | **Create and read reports** | `/report` · `/reports` · `/show REPORT_ID` | [Using DataChat § 2](docs/user-guide/02-using-datachat.md#task-2--create-and-read-reports) |
| 🗑️ | **Delete your reports** (with confirmation) | `/delete conversation` → `DELETE OPERATION_ID` | [Using DataChat § 3](docs/user-guide/02-using-datachat.md#task-3--delete-reports) |

> [!IMPORTANT]
> DataChat understands a **fixed set of approved questions and follow-ups**, not free-form
> English. This is deliberate: every accepted phrase maps to a query that can be checked exactly.
> Type `/help` at any time to see the full list.

## 👥 Who you can be

There are three executive identities. Each one sees a **different, non-overlapping** part of the
product catalog:

| Identity | Executive | Products visible | Hidden from this identity |
|---|---|---|---|
| 🟦 `user-a` | User A | **Men's** department | Accessories, all Women's products |
| 🟪 `user-b` | User B | **Women's** department | Accessories, all Men's products |
| 🟩 `user-c` | User C | **Accessories** (both departments) | All other categories |

You choose the identity when you start DataChat (`--user user-b`). You can't switch identity in
the middle of a chat. See [Roles & Security](docs/user-guide/03-roles-and-security.md) for details.

## 🚀 Quick start (Docker)

You need **Git**, **Docker** (with Compose), the **gcloud CLI**, **uv**, a **Gemini API key** and a
**Google Cloud project** with BigQuery enabled. The [installation guide](docs/user-guide/01-installation.md)
explains how to get each one.

```bash
# 1. Get the project
git clone https://github.com/delaray/opsfleet.git datachat && cd datachat

# 2. Configure: add your Gemini key, GCP project and a database password
cp .env.example .env && nano .env

# 3. Let BigQuery use your Google account
gcloud auth application-default login

# 4. Package your credentials as Docker secrets (stored in ./secrets, never in the image)
uv run --no-project --with python-dotenv python -m scripts.make_docker_secrets

# 5. Download the public image, start the report database, then chat
docker compose pull        # pulls delaray/datachat:1.0.1 from Docker Hub
docker compose up -d postgres
docker compose run --rm datachat --chat --user user-a
```

When the `You>` prompt appears, try:

```text
You> Show item counts by category
You> completed items only
You> /report
You> /quit
```

> [!TIP]
> Prefer running without Docker? The [installation guide](docs/user-guide/01-installation.md#option-b--local-python-installation)
> also covers a local Python install with **uv** and **PostgreSQL**.

## 📚 Documentation

| Guide | Read it when you want to… |
|---|---|
| 🛠️ [**1. Installation**](docs/user-guide/01-installation.md) | Install DataChat with Docker or Python, and check that it works |
| 💬 [**2. Using DataChat**](docs/user-guide/02-using-datachat.md) | Learn the questions, follow-ups and report commands, with a one-page cheat sheet |
| 🔐 [**3. Roles & Security**](docs/user-guide/03-roles-and-security.md) | Understand identities, authentication, validation, privacy and audit |
| 🎬 [**4. Examples**](docs/user-guide/04-examples.md) | Follow complete sessions, from first question to deleted report |
| 🩺 [**5. Troubleshooting & FAQ**](docs/user-guide/05-troubleshooting.md) | Understand an error message, limits, quotas and tips |

## 📦 Assignment deliverables

The [deliverables/](deliverables/) folder holds the completed assignment: the brief, the design documents
(each as Markdown, Word and PDF) and the architecture diagrams they reference.

| File | Type | Description |
|---|---|---|
| [datachat-assignment.pdf](deliverables/datachat-assignment.pdf) | PDF | Another copy of the assignment brief (same text, separately exported file) |
| [datachat-agent-hld-requirements-and-integration-design.md](deliverables/datachat-agent-hld-requirements-and-integration-design.md) | Markdown | Production High-Level Design: requirement coverage matrix, component relationships, design decisions and conclusions |
| [datachat-agent-hld-requirements-and-integration-design.docx](deliverables/datachat-agent-hld-requirements-and-integration-design.docx) | Word | Word version of the High-Level Design |
| [datachat-agent-hld-requirements-and-integration-design.pdf](deliverables/datachat-agent-hld-requirements-and-integration-design.pdf) | PDF | PDF version of the High-Level Design |
| [datachat-agent-logical-architecture-explained.md](deliverables/datachat-agent-logical-architecture-explained.md) | Markdown | Companion to the HLD that explains each of the 17 components in the logical architecture diagram: purpose, operation, inputs, outputs and controls |
| [datachat-agent-logical-architecture-explained.docx](deliverables/datachat-agent-logical-architecture-explained.docx) | Word | Word version of the logical architecture explanation |
| [datachat-agent-logical-architecture-explained.pdf](deliverables/datachat-agent-logical-architecture-explained.pdf) | PDF | PDF version of the logical architecture explanation |
| [datachat-design-and-implentation-plan.md](deliverables/datachat-design-and-implentation-plan.md) | Markdown | Architecture, design strategy and implementation roadmap: technology stack, LangGraph agent design, how each requirement is met, build order and demo plan |
| [datachat-design-and-implentation-plan.docx](deliverables/datachat-design-and-implentation-plan.docx) | Word | Word version of the design and implementation plan |
| [datachat-design-and-implentation-plan.pdf](deliverables/datachat-design-and-implentation-plan.pdf) | PDF | PDF version of the design and implementation plan |
| [hligh-level-logical-architecture..png](<deliverables/hligh-level-logical-architecture..png>) | PNG image | High-level logical architecture: UI, API gateway, LangGraph orchestrator, intent router, analysis subgraph, report management and the data stores behind them |
| [logical-architecture.png](deliverables/logical-architecture.png) | PNG image | Detailed logical architecture with the 17 components described in the architecture explanation document |
| [golden-bucket-feedback-loop.png](<deliverables/golden-bucket-feedback-loop.png>) | PNG image | Golden Bucket feedback loop: captured analyses go through quality checks and analyst review, then into the versioned Golden Bucket, followed by re-indexing of embeddings |
| [Runtime-Dataflow-Diagram.png](deliverables/Runtime-Dataflow-Diagram.png) | PNG image | Runtime dataflow: how a single request moves through the system at query time |
| [retrieval-architecture.png](deliverables/retrieval-architecture.png) | PNG image | Retrieval architecture: how Golden Knowledge (Trios), schema and metric context are fetched for a question |
| [deletion-workflow.png](deliverables/deletion-workflow.png) | PNG image | Destructive-operation workflow: deleting a saved report with scoped targeting and explicit human confirmation |
| [poc-architecture.png](deliverables/poc-architecture.png) | PNG image | Architecture of the proof-of-concept prototype built in this repository |
| [gcp-deployment.png](deliverables/gcp-deployment.png) | PNG image | Production GCP deployment: Cloud Run, Cloud SQL, Cloud Storage, Vertex AI, BigQuery, plus security and operations |
| [gcp-deployment.svg](deliverables/gcp-deployment.svg) | SVG image | Vector (scalable) version of the GCP deployment diagram |
| [gcp-deployment-legend.png](deliverables/gcp-deployment-legend.png) | PNG image | Legend for the GCP deployment diagram, showing what each service color group handles |
| [datachat-users.png](deliverables/datachat-users.png) | PNG image | Table of the three executive users (A, B, C) and the product categories each one is allowed to see |

<details>
<summary><b>For developers and reviewers</b></summary>

| Document | Topic |
|---|---|
| [docs/README.dev.md](docs/README.dev.md) | Developer README: implementation status, graph wiring, tests |
| [ARCHITECTURE.md](ARCHITECTURE.md) | Production high-level design |
| [TRUSTED_EXECUTOR.md](TRUSTED_EXECUTOR.md) | The restricted BigQuery executor |
| [REPORTS.md](REPORTS.md) | Report generation, ownership and the deletion workflow |
| [OBSERVABILITY.md](OBSERVABILITY.md) | Tracing, budgets and debugging |
| [docs/postgresql.md](docs/postgresql.md) · [docs/docker-image.md](docs/docker-image.md) | Database and container details |

</details>

---

<div align="center">
<sub>DataChat is a proof of concept. The <code>--user</code> option selects a test identity on your own machine; it is not a login system.
See <a href="docs/user-guide/03-roles-and-security.md">Roles &amp; Security</a>.</sub>
</div>
