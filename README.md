# Nexus Search API

A high-performance semantic search engine built in **Go**. This system leverages **PostgreSQL + pgvector** for vector storage and **Ollama** for local LLM inference to provide a scalable, API-first solution for semantic data retrieval.

## 🏗 System Architecture

The architecture follows **Clean Architecture** principles, designed to be domain-agnostic—capable of indexing anything from source code repositories to movie databases.

*   **API Layer:** Headless REST endpoints built for microservices.
*   **Inference Layer:** Integrates with local **Ollama** using the `mxbai-embed-large` model (1024-dimensional embeddings).
*   **Data Layer:** PostgreSQL with the `pgvector` extension utilizing **HNSW (Hierarchical Navigable Small World)** indexing for sub-linear search latency.
*   **Orchestration:** Containerized with Docker; Kubernetes-ready for Phase 2 scaling.

## 🛠 Tech Stack

| Component | Technology |
| :--- | :--- |
| **Language** | Go 1.22+ |
| **Database** | PostgreSQL + `pgvector` |
| **AI/Inference** | Ollama (`mxbai-embed-large`) |
| **Containerization** | Docker / Docker Compose |
| **Orchestration** | Kubernetes (Phase 2) |
| **Drivers** | pgx (High-performance PostgreSQL) |

## 🚀 Getting Started

### 1. Local Infrastructure (Docker Compose)
To spin up the database and AI engine locally:

```yaml
services:
  db:
    image: ankane/pgvector:latest
    ports: ["5432:5432"]
    environment:
      POSTGRES_USER: anmol
      POSTGRES_PASSWORD: password
      POSTGRES_DB: nexus_db

  ollama:
    image: ollama/ollama:latest
    ports: ["11434:11434"]
```

### 2. Ingestion Strategy
The engine supports a flexible ingestion pipeline:
*   **Source Code Search:** Walking local repositories (e.g., Linux kernel) to extract and index logic patterns.
*   **Media Discovery:** Fetching and indexing metadata from external APIs (TMDB/OMDb) for semantic plot search.

## 🔌 API Reference

### Index Content
`POST /api/index`
```json
{
  "content": "func Search(query string) ([]Result, error)",
  "metadata": {
    "source": "linux-kernel",
    "language": "go"
  }
}
```

### Semantic Search
`POST /api/search`
```json
{
  "query": "How are search queries handled?",
  "limit": 5
}
```

## 📂 Project Structure
```text
├── cmd/api/            # Entry point
├── internal/
│   ├── domain/         # Entities & Interfaces
│   ├── usecase/        # Business Logic
│   ├── repository/     # pgvector/Postgres implementation
│   └── handler/        # HTTP Handlers
├── k8s/                # Kubernetes Manifests (Phase 2)
├── pkg/                # Reusable packages (Ollama client)
└── migrations/         # SQL schema versions (1024-dim vectors)
```

## 📜 License
## 📜 License
This project is licensed under the **GNU General Public License v3.0**. See the [LICENSE](LICENSE) file for details.
