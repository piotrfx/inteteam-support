# Phase 2: RAG Chat System — Implementation Plan

## Overview

AI-powered chat widget embedded in each InteTeam app. Uses retrieval-augmented generation (RAG) to answer tenant questions from the user manual content in `inteteam-support`.

**Status:** Ready to build — Phase 1 content exists (34 CRM admin guides).

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│  InteTeam App (CRM / Panel / CMS / SSO)                     │
│                                                              │
│  ┌──────────────────────────────────┐                       │
│  │  <ChatWidget />                   │                       │
│  │  - Knows: app, role, current page │                       │
│  │  - Sends: question + context      │                       │
│  └──────────┬───────────────────────┘                       │
└─────────────┼───────────────────────────────────────────────┘
              │ POST /api/chat
              ▼
┌─────────────────────────────────────────────────────────────┐
│  inteteam-rag (new service)          Port: 3100              │
│  Node.js / TypeScript API                                    │
│                                                              │
│  1. Embed question (via Ollama embedding model)              │
│  2. Vector search (pgvector) — top 5 chunks                  │
│  3. Filter by app + role metadata                            │
│  4. Build prompt: system + chunks + question                 │
│  5. Stream answer from Ollama 7B model                       │
│  6. Return streamed response to widget                       │
│                                                              │
│  Also:                                                       │
│  - POST /api/ingest — re-index docs from git repo            │
│  - GET  /api/health — service status                         │
└──────┬──────────────────────────┬───────────────────────────┘
       │                          │
       ▼                          ▼
┌──────────────┐    ┌──────────────────────────────────┐
│  Ollama       │    │  PostgreSQL + pgvector             │
│  Port: 11434  │    │  Port: 5433 (avoid 5432 clash)     │
│               │    │                                    │
│  Models:      │    │  Tables:                           │
│  - llama3:8b  │    │  - doc_chunks (content, embedding, │
│  - nomic-embed│    │    metadata, app, role, source)     │
│               │    │  - chat_sessions (conversation log) │
└──────────────┘    └──────────────────────────────────┘
```

---

## Infrastructure (Dell R550)

### What we have
- Proxmox hypervisor with LXC + QEMU
- No GPU (CPU-only inference)
- PostgreSQL 16 running in inteteam-panel Docker network
- Ports taken: 3000, 5432, 8000, 8081, 8082

### What we need to add
| Component | How | Port |
|-----------|-----|------|
| **Ollama** | Docker container or bare metal | 11434 |
| **inteteam-rag** | Docker container (new service) | 3100 |
| **PostgreSQL + pgvector** | New container (separate from panel's DB) | 5433 |

### Why separate PostgreSQL?
Panel's PostgreSQL is isolated in its Docker network and has no pgvector extension. Cleaner to spin up a dedicated instance with pgvector pre-installed than to modify panel's setup.

### CPU Inference Performance (no GPU)
With a Dell R550 (likely Xeon, 64GB+ RAM):
- **Embedding** (nomic-embed-text): ~50ms per query, ~200ms per chunk batch — fast enough
- **LLM generation** (llama3:8b Q4): ~5-15 tokens/sec on CPU — acceptable for chat, ~3-8 second responses
- **Memory**: Ollama needs ~6GB for the 8B model + ~500MB for embedding model

---

## Decisions Made

### Embedding Model: nomic-embed-text

- 274MB, 768-dimension vectors
- Good balance of quality and speed
- Runs via Ollama (same infrastructure as LLM)
- Handles markdown/technical content well

### LLM: Llama 3 8B (Q4_K_M quantization)

- 5GB on disk, ~6GB in memory
- Best quality in the 7-8B range
- Good instruction following for Q&A
- Runs on CPU acceptably (5-15 tok/s)

### Vector Store: PostgreSQL 16 + pgvector

- Proven, reliable, SQL-based
- Perfect for our scale (<5,000 chunks)
- HNSW index for fast approximate search
- No new technology to learn — standard PostgreSQL

### Service: New standalone — inteteam-rag

Not extending inteteam-mcp because:
- MCP uses stdio transport (for Claude Desktop) — wrong protocol for a web API
- RAG needs HTTP endpoints for chat widgets
- Different lifecycle — MCP is developer tooling, RAG is tenant-facing

---

## New Project: inteteam-rag

### Stack

| Component | Technology |
|-----------|-----------|
| Runtime | Node.js 20 + TypeScript |
| HTTP framework | Fastify (lightweight, fast, streaming support) |
| Ollama client | `ollama` npm package |
| PostgreSQL client | `pg` + raw SQL (pgvector queries are simple) |
| Markdown parser | `marked` or `markdown-it` (for chunking) |
| Streaming | Server-Sent Events (SSE) to chat widget |

### Project Structure

```
inteteam-rag/
  src/
    index.ts              # Fastify server, routes
    config.ts             # Environment config
    ingest/
      chunker.ts          # Markdown -> chunks with metadata
      embedder.ts         # Ollama embedding calls
      indexer.ts           # Store chunks + vectors in pgvector
      git-sync.ts         # Pull latest docs from inteteam-support repo
    chat/
      retriever.ts        # Vector search + metadata filtering
      prompt-builder.ts   # System prompt + context assembly
      streamer.ts         # Ollama chat completion, SSE stream
    db/
      migrations/         # pgvector setup, tables
      pool.ts             # PostgreSQL connection pool
  docker-compose.yml      # rag service + postgres + ollama
  Dockerfile
  .env.example
```

### API Endpoints

| Method | Path | Purpose | Auth |
|--------|------|---------|------|
| POST | `/api/chat` | Ask a question, get streamed answer | API key per app |
| POST | `/api/ingest` | Re-index docs from git | Admin key |
| GET | `/api/health` | Service status + model info | None |

### Chat Request

```json
POST /api/chat
{
  "question": "How do I print a barcode label?",
  "app": "inteteam_crm",
  "role": "admin",
  "page": "/admin/bookings/123",
  "session_id": "optional-for-conversation-history"
}
```

### Chat Response (SSE stream)

```
data: {"type": "chunk", "content": "To print a barcode label"}
data: {"type": "chunk", "content": ", open any booking"}
data: {"type": "chunk", "content": " and click the **Print Label** button."}
data: {"type": "sources", "sources": [{"title": "Printing Labels", "url": "/docs/21-label-printing"}]}
data: {"type": "done"}
```

---

## Content Pipeline

### Ingestion Flow

```
1. git pull inteteam-support (or webhook on push)
2. Scan docs/{app}/*.md files
3. For each file:
   a. Parse markdown
   b. Split into chunks by H2/H3 headings
   c. Prefix each chunk with parent heading for context
   d. Extract metadata: app, roles (from filename/path), feature name
   e. Generate embedding via Ollama nomic-embed-text
   f. Upsert into pgvector (replace old chunks for same source file)
```

### Chunking Strategy

```markdown
# Printing Labels                         <- H1 = document title (metadata)

## Where to Print From                    <- H2 = chunk boundary

The **Print Label** button appears on...  <- chunk content
                                             (includes H2 as prefix)

## How to Print                           <- H2 = next chunk boundary

1. Open the booking...                    <- next chunk content
```

Each chunk gets:
- `content`: heading + body text (~300-800 tokens)
- `embedding`: 768-dim vector from nomic-embed-text
- `app`: which app (e.g. "inteteam_crm")
- `roles`: which roles this applies to (e.g. ["admin", "technician"])
- `source_file`: path for deduplication (e.g. "inteteam_crm/21-label-printing.md")
- `section`: heading text for display
- `url`: link back to full doc

### Database Schema

```sql
CREATE EXTENSION IF NOT EXISTS vector;

CREATE TABLE doc_chunks (
    id          BIGSERIAL PRIMARY KEY,
    app         TEXT NOT NULL,
    roles       TEXT[] NOT NULL DEFAULT '{}',
    source_file TEXT NOT NULL,
    section     TEXT NOT NULL,
    content     TEXT NOT NULL,
    embedding   vector(768) NOT NULL,
    url         TEXT,
    created_at  TIMESTAMPTZ DEFAULT NOW(),
    updated_at  TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_chunks_embedding ON doc_chunks
    USING hnsw (embedding vector_cosine_ops);

CREATE INDEX idx_chunks_app ON doc_chunks (app);

CREATE TABLE chat_sessions (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    app         TEXT NOT NULL,
    user_role   TEXT,
    messages    JSONB NOT NULL DEFAULT '[]',
    created_at  TIMESTAMPTZ DEFAULT NOW(),
    updated_at  TIMESTAMPTZ DEFAULT NOW()
);
```

### Retrieval Query

```sql
SELECT section, content, source_file, url,
       1 - (embedding <=> $1::vector) AS similarity
FROM doc_chunks
WHERE app = $2
  AND ($3 = ANY(roles) OR roles = '{}')
ORDER BY embedding <=> $1::vector
LIMIT 5;
```

---

## Chat Widget (React Component)

Shared across all InteTeam apps. A single React component that can be dropped into any layout.

### Props

```typescript
interface ChatWidgetProps {
    apiUrl: string;          // RAG service URL
    apiKey: string;          // Per-app API key
    app: string;             // "inteteam_crm", "inteteam_panel", etc.
    userRole?: string;       // "admin", "team", etc.
    currentPage?: string;    // Current route for context
}
```

### Behaviour

- **Floating button** (bottom-right corner) with chat icon
- Click opens a **slide-out panel** (right side, 400px wide)
- Message input at bottom, messages scroll above
- Answers stream in token-by-token (SSE)
- Sources shown as clickable links below each answer
- Conversation persists within the browser session
- "This didn't help" button on each answer (future: creates ticket)

### Publishing

Option A: **npm package** (`@inteteam/chat-widget`) — install in each app
Option B: **Script tag embed** — `<script src="https://rag.inte.team/widget.js">` (simpler for non-React apps)

Recommend **Option A** for our React apps (CRM, Panel, CMS all use React).

---

## Prompt Template

```
You are a helpful support assistant for InteTeam {app_name}.
The user has the "{role}" role and is currently on the "{page}" page.

Answer their question using ONLY the documentation provided below.
If the documentation doesn't contain the answer, say so honestly.
Keep answers concise and practical — use numbered steps for how-to questions.
Link to relevant documentation sections when available.

---
Documentation:
{retrieved_chunks}
---

User question: {question}
```

---

## Docker Compose (inteteam-rag)

```yaml
services:
  rag-api:
    build: .
    ports:
      - "3100:3100"
    environment:
      - DATABASE_URL=postgresql://rag:password@rag-db:5432/inteteam_rag
      - OLLAMA_URL=http://ollama:11434
      - DOCS_REPO_PATH=/data/inteteam-support
    volumes:
      - docs-data:/data
    depends_on:
      - rag-db
      - ollama
    restart: unless-stopped

  rag-db:
    image: pgvector/pgvector:pg16
    ports:
      - "5433:5432"
    environment:
      - POSTGRES_DB=inteteam_rag
      - POSTGRES_USER=rag
      - POSTGRES_PASSWORD=password
    volumes:
      - pgdata:/var/lib/postgresql/data
    restart: unless-stopped

  ollama:
    image: ollama/ollama:latest
    ports:
      - "11434:11434"
    volumes:
      - ollama-models:/root/.ollama
    restart: unless-stopped

volumes:
  pgdata:
  ollama-models:
  docs-data:
```

---

## Implementation Order

### Step 1: Infrastructure (1 session)
- [ ] Docker Compose with Ollama + PostgreSQL/pgvector
- [ ] Pull and test models: `ollama pull llama3:8b` + `ollama pull nomic-embed-text`
- [ ] Verify CPU inference speed on Dell R550

### Step 2: Ingestion pipeline (1 session)
- [ ] Markdown chunker (split by H2/H3, extract metadata)
- [ ] Embedding via Ollama API
- [ ] Store in pgvector
- [ ] Ingest all 34 CRM guides
- [ ] Test retrieval quality with sample questions

### Step 3: Chat API (1 session)
- [ ] Fastify server with `/api/chat` endpoint
- [ ] Retriever: vector search + role/app filtering
- [ ] Prompt builder: system prompt + chunks + question
- [ ] Streamed response via SSE
- [ ] Session management (optional conversation context)

### Step 4: Chat widget (1 session)
- [ ] React component: floating button + slide-out panel
- [ ] SSE client for streaming responses
- [ ] Source links display
- [ ] Integrate into inteteam_crm as first app

### Step 5: Production deploy (1 session)
- [ ] Deploy on Dell R550
- [ ] Caddy/NPM reverse proxy for rag.inte.team
- [ ] API keys per app
- [ ] Auto-ingest on git push (webhook or cron)
- [ ] Monitor inference speed and adjust model if needed

---

## Cost

**Zero ongoing cost** — everything self-hosted:
- Ollama: free, open-source
- Models: free (Llama 3, nomic-embed-text)
- PostgreSQL + pgvector: free
- Only cost: Dell R550 electricity + ~6GB RAM for models
