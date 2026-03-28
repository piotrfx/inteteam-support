# Phase 2: RAG Chat System — Planning

## Overview

AI-powered chat widget embedded in each InteTeam app. Uses retrieval-augmented generation (RAG) to answer tenant questions from the user manual content stored in inteteam_cms.

**Status:** Planning — depends on Phase 1 (user manual) content existing first.

---

## Architecture

```
Chat widget (in each app)
    -> POST question to API
    -> Embedding model converts question to vector
    -> Vector search against manual content chunks
    -> Top-k relevant chunks retrieved
    -> 7B LLM generates answer from chunks + question
    -> Response streamed back to widget
    -> If low confidence -> offer ticket creation (Phase 3)
```

---

## Key Decisions (To Be Made)

### Embedding Model

| Option | Size | Quality | Notes |
|--------|------|---------|-------|
| bge-small-en-v1.5 | 130MB | Good | Fast, small footprint |
| nomic-embed-text | 274MB | Better | Good for longer docs |
| bge-large-en-v1.5 | 1.3GB | Best | Slower, more accurate |

### LLM

| Option | Size | Quality | Notes |
|--------|------|---------|-------|
| Mistral 7B | 4GB (Q4) | Good | Fast, good instruction following |
| Llama 3 8B | 5GB (Q4) | Better | Newer, better reasoning |
| Phi-3 Mini 3.8B | 2GB (Q4) | Decent | Smallest option, fastest |

### Vector Store

| Option | Notes |
|--------|-------|
| pgvector (PostgreSQL extension) | Reuse existing infra, good for <100k chunks |
| Qdrant | Purpose-built, better performance at scale |
| ChromaDB | Simple, Python-native, good for prototyping |

### Hosting

- Self-hosted on existing Dell R550 (has GPU?)
- Or external API (Ollama + existing server)
- Consider: inference latency vs cost

---

## Content Pipeline

```
Manual page created/updated in CMS
    -> Webhook triggers re-indexing
    -> Page split into chunks (500-1000 tokens each)
    -> Each chunk embedded via embedding model
    -> Vectors stored with metadata (app, role, feature, page URL)
    -> Old vectors for that page replaced
```

### Chunking Strategy

- Split by heading (H2/H3 sections)
- Include parent heading as context prefix
- Overlap: 50 tokens between chunks
- Metadata: app name, role tags, feature name, source URL

---

## Chat Widget

### Placement
- Floating button (bottom-right) in every InteTeam app
- Opens slide-out panel or modal
- Preserves conversation within session

### Context Awareness
- Widget knows which app the user is in
- Knows which page/route (for better retrieval)
- Knows user role (for filtering relevant content)

### UX Flow
1. User clicks chat icon
2. Types question: "How do I print a barcode label?"
3. System retrieves relevant manual chunks
4. 7B model generates step-by-step answer with links to full docs
5. If answer insufficient: "Would you like to create a support ticket?"

---

## Integration with inteteam-mcp

The existing MCP server (`inteteam-mcp`) can be extended to:
- Serve as the RAG API endpoint
- Handle embedding generation
- Manage vector store queries
- Route to LLM for generation

This keeps the AI infrastructure in one place.

---

## Prototype Plan

Before building the full system:
1. Take 10 existing feature docs from `inteteam_crm/docs/features/`
2. Chunk and embed them
3. Test retrieval quality with 20 sample questions
4. Evaluate: does a 7B model give useful answers from these chunks?
5. If yes -> proceed with full manual + production RAG
6. If no -> identify gaps (content quality? chunking? model size?)
