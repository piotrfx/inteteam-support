# InteTeam Support

Multi-project support infrastructure for the InteTeam platform. Provides self-service documentation, AI-assisted chat, and ticket-based escalation across all InteTeam apps.

## Problem

500+ repair shops using InteTeam CRM (and growing). Each shop has staff turnover. New hires need onboarding. Features like barcode scanning, label printing, booking workflows, inventory management are not self-explanatory. Manual onboarding and ad-hoc support doesn't scale. As the platform expands (CRM, SSO, Panel, future apps), the support burden multiplies.

## Solution

Three layers, built in order:

### Phase 1: User Manual

Structured documentation hosted in `inteteam_cms`. Written per-app, per-role. Covers every feature a tenant user needs to operate the system. This is the foundation — everything else depends on quality content existing first.

### Phase 2: AI Chat (RAG)

Connect `inteteam-mcp` to the manual content. Use a 7B model with an embedding model to provide instant answers to tenant questions. Embedded as a chat widget inside each InteTeam app. Answers the easy 80% — "how do I print a label?", "how do I scan a barcode?", "how do I move a booking to undergoing?".

### Phase 3: Ticket System

When RAG can't answer or the user has a bug/problem, escalate to a ticket. Integrated into each app. Structured reports instead of vague messages. Gives visibility into what needs fixing and what documentation is missing.

---

## Platform Apps

| App | Repo | Status | Purpose |
|-----|------|--------|---------|
| InteTeam CRM | `inteteam_crm` | Production | Multi-tenant repair management (500+ shops) |
| InteTeam SSO | `inteteam_sso` | In Development | Single sign-on across all InteTeam apps |
| InteTeam Panel | `inteteam-panel` | Production | Hosting control panel |
| InteTeam CMS | `inteteam_cms` | Production | Content management — will host the user manual |
| InteTeam MCP | `inteteam-mcp` | Production | MCP server for CRM integration — will power RAG |
| Print Bridge | `inteteam-print-bridge` | Production | Local print agent for shops |

---

## Documentation Structure

```
inteteam-support/
  README.md                  # This file — project overview and phases
  docs/
    inteteam_crm/            # CRM user manual planning
    inteteam_sso/            # SSO user manual planning
    inteteam-panel/          # Panel user manual planning
    inteteam_cms/            # CMS user manual planning
    inteteam-print-bridge/   # Print Bridge setup guide planning
    rag-architecture/        # Phase 2: AI chat system design
    ticket-system/           # Phase 3: Ticket system design
```

Each `docs/{app}/` folder will contain:
- `README.md` — scope, roles, feature list for that app's manual
- Role-based guides (admin, technician, warehouse, manager)
- Feature walkthroughs with screenshots

---

## Phase 1: User Manual — Approach

### Content Sources

Most content already exists in structured form:

| Source | Location | What it contains |
|--------|----------|-----------------|
| Feature docs | `inteteam_crm/docs/features/` | Business requirements, user stories, acceptance criteria |
| Feature docs | Per-feature `README.md` | UX flows, step-by-step descriptions |
| CLAUDE.md files | Each project root | Architecture, key concepts |
| inte-playbook | `inte-playbook/` | Conventions, patterns (internal — not for tenants) |

The feature docs contain user stories that translate almost directly into manual content. The task is to restructure them from developer-facing to user-facing.

### Writing Principles

- **Role-based:** A shop technician doesn't need admin setup docs. Separate by role.
- **Task-oriented:** "How to print a barcode label", not "PrintBarcodeButton component".
- **Screenshot-heavy:** Show, don't just tell. Every walkthrough needs visuals.
- **Searchable:** Short titles, clear headings, consistent terminology.
- **Versioned:** Manual updates must follow feature releases.

### Roles (CRM)

| Role | Sees | Typical tasks |
|------|------|---------------|
| Shop Technician | Team dashboard | Scan barcodes, view bookings, update repair status |
| Warehouse Worker | Inventory pages | Print labels, manage stock, scan items |
| Shop Manager | Admin dashboard | Manage bookings, assign work, view reports, configure settings |
| Company Admin | Admin + settings | User management, printer setup, company settings, billing |

---

## Phase 2: RAG Chat — High-Level Plan

### Architecture

```
Tenant user asks question in chat widget
    -> Question sent to inteteam-mcp
    -> Embedding model converts to vector
    -> Vector search against manual content (stored in inteteam_cms)
    -> Top-k relevant chunks retrieved
    -> 7B model generates answer from chunks
    -> Answer displayed in chat widget
    -> If confidence low or user unsatisfied -> offer to create ticket (Phase 3)
```

### Key Decisions (to be made)

- Embedding model choice (e.g. bge-small, nomic-embed)
- 7B model choice (e.g. Mistral 7B, Llama 3 8B)
- Vector store (pgvector in existing PostgreSQL, or dedicated like Qdrant)
- Hosting: self-hosted on existing infrastructure or external
- Chat widget: embedded in each app's layout or standalone

### Content Pipeline

Manual content in CMS -> chunked + embedded on publish -> stored in vector DB -> queried at chat time

---

## Phase 3: Ticket System — High-Level Plan

### Requirements

- Embedded in each InteTeam app (not a separate portal)
- Pre-filled context: which app, which page, which company, which user
- Structured categories: bug report, feature request, how-to question, account issue
- RAG attempted first — ticket created only when AI can't resolve
- Dashboard for InteTeam team to manage tickets across all apps
- Status tracking visible to tenant (open, in progress, resolved)

### Integration Points

- Chat widget escalation ("This didn't help" -> create ticket with chat history attached)
- Direct "Report Problem" button in each app's nav/footer
- Email notifications on status changes

---

## Next Steps

1. Start with `docs/inteteam_crm/README.md` — scope the CRM manual, list all features by role
2. Write the first few guides for high-traffic features (bookings, barcode scanning, label printing)
3. Decide on CMS content structure (how manual pages are stored/organized in inteteam_cms)
4. Prototype RAG with existing feature docs to validate the approach before writing the full manual
