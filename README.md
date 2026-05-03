# Semantic Search Engine

A semantic search engine built in **Go**, using **PostgreSQL + pgvector** for vector storage and **Ollama** for local LLM inference. Designed to be domain-agnostic — the same pipeline works across any text corpus.

> **Status:** Phase 1 — core engine under active development.

---

## 🏗 System Architecture

Follows **Clean Architecture** principles with a clear separation between layers.

- **API Layer:** Headless endpoints built for microservices (REST / gRPC — TBD)
- **Inference Layer:** Local **Ollama** using `mxbai-embed-large` (1024-dimensional embeddings)
- **Data Layer:** PostgreSQL with `pgvector` extension and **HNSW** indexing for high-scale retrieval
- **Execution Strategy:** Local-first in Phase 1 using native host services for the fastest feedback loop — containerisation (Docker) and orchestration (K8s) once the core engine stabilises

---

## 🛠 Tech Stack

| Component | Technology |
| :--- | :--- |
| **Language** | Go (Golang) |
| **Database** | PostgreSQL + `pgvector` |
| **AI / Inference** | Ollama (`mxbai-embed-large`) |
| **Indexing** | HNSW (Hierarchical Navigable Small World) |
| **Orchestration** | Kubernetes (Phase 3) |
| **License** | GPL v3 |

---

## 🚀 Phase 1: Core Development Setup

### 1. Prerequisites

Make sure these are running on your machine:

- Go 1.21+
- PostgreSQL with [pgvector](https://github.com/pgvector/pgvector) extension
- Ollama with `mxbai-embed-large` pulled

```bash
ollama pull mxbai-embed-large
```

### 2. Database Setup

```sql
-- Enable vector extension
CREATE EXTENSION IF NOT EXISTS vector;

-- Documents table optimised for mxbai-embed-large (1024-dim)
CREATE TABLE documents (
    id        SERIAL PRIMARY KEY,
    content   TEXT NOT NULL,
    metadata  JSONB,
    embedding vector(1024)
);

-- HNSW index for cosine similarity
CREATE INDEX ON documents USING hnsw (embedding vector_cosine_ops);
```

### 3. Environment

```bash
cp .env.example .env
```

```env
DB_URL=postgres://user:password@localhost:5432/nexus_db
OLLAMA_URL=http://localhost:11434
```

### 4. Run

```bash
go run ./cmd/server
```

---

## 🗺 Roadmap

### Phase 1: Core Engine *(current)*
- [x] Project initialisation, licence set (GPL v3)
- [ ] Database schema and migration logic
- [ ] Ollama embedding client (1024-dim)
- [ ] Ingestion service — content to vector
- [ ] Semantic search service — cosine distance queries
- [ ] API layer decision and implementation (REST / gRPC)

### Phase 2: Containerisation
- [ ] Multi-stage `Dockerfile` for Go service
- [ ] `docker-compose.yml` for full stack replication
- [ ] Resource profiling — memory and CPU under load

### Phase 3: Orchestration
- [ ] Kubernetes manifests (Deployment / Service)
- [ ] Monitoring once resource baseline is established

---

## 📊 Benchmarking Goals

One of the core motivations for building this in Go is measuring how Go's concurrency primitives (goroutines, channels) affect vector search throughput at different dataset sizes compared to equivalent Python implementations.

Specifically:
- Query latency at varying HNSW `ef_search` parameters
- Embedding throughput under concurrent ingestion load
- Memory footprint vs Python (sentence-transformers + psycopg2)

Results will be documented in [`BENCHMARKS.md`](./BENCHMARKS.md) once the core pipeline is stable.

---

## 📜 License

Licensed under the **GNU General Public License v3.0** — see [LICENSE](./LICENSE) for details.

---

*Built by [Anmol Dubey](https://github.com/dubeyanmol)*
