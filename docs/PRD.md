# Inte.Team Support Platform — PRD

**Status:** Planning  
**Date:** 2026-06-10  
**Team:** piotrfx, Patryk, Lukasz, web dev team (4 devs + 1 apprentice)

---

## 1. Overview

Inte.Team Support is a **standalone, branded support platform** for InteTeam customers and their customers. It replaces ad-hoc TeamViewer, email chains, and Slack with a unified, context-aware support workspace.

This is a **B2B2C product**:
- InteTeam sells support capability to business owners (repair shops, web dev clients, etc.)
- Those business owners use it to support their own customers
- InteTeam charges the business owner based on resource usage (wholesale)
- The business owner charges their customers however they choose (retail — not InteTeam's concern)

This is not a CRM module. It is its own product, deployed independently, integrated via SSO and thin APIs.

---

## 2. Problem

- Web dev customers have hardware (printers, scanners) managed through the CRM. Remote help requires jumping out of the InteTeam ecosystem into TeamViewer — no record, no brand.
- Ticket management is ad-hoc. No structured view of what's open, in progress, or resolved.
- Not every customer wants remote desktop. Customers who need async help have no structured channel.
- As InteTeam scales to 500+ shops, manual support won't scale. The system must support self-service, escalation, and direct technical assistance in one place.
- Business owners want to offer tiered support to their own customers without InteTeam involvement in day-to-day operations.

---

## 3. Target Users

| User | Role |
|---|---|
| InteTeam IT support engineer | Handles tickets, chats, and remote sessions across all tenants |
| InteTeam web dev | Diagnoses hardware issues (printers, scanners) via remote desktop |
| Business owner / company admin (tenant) | Configures their support workspace, manages their customer groups |
| Business owner's end customer | Raises tickets, starts chat, optionally requests remote help |

---

## 4. Out of Scope

- Replacing the RAG chat widget embedded in the CRM (stays in `inteteam-rag`)
- Hosting the user manual (lives in `inteteam_cms`)
- Payment processing (we track usage; billing is a separate future concern)
- Mobile native apps (web-first, mobile-responsive)

---

## 5. Platform Architecture

```
inteteam-support          (this repo — Laravel 12 + React 19 + Inertia)
    └── Ticket System     Phase 1
    └── Live Chat         Phase 2
    └── Remote Desktop    Phase 3  ←─ connects to inteteam-remote

inteteam-remote           (separate Go repo)
    └── Signaling Server  WebRTC session broker (Go + Pion)
    └── TURN relay        Fallback for restrictive firewalls
    └── Desktop Agent     Compiled binary (Windows first, then Linux/macOS)

inteteam-sso              (existing)
    └── Identity layer    All auth flows for all InteTeam apps

inteteam-panel            (existing)
    └── Provisioning hub  Creates tenant accounts, assigns tiers, sets usage limits

inteteam_crm              (existing)
    └── "Open Support"    One button → SSO session → inteteam-support
```

### Provisioning flows

```
Path A (self-registration):
  Customer → registration form on support.inte.team
           → SSO account created
           → Panel notified via API
           → Panel assigns default tier + limits

Path B (CRM auto-provision):
  New company created in CRM
           → CRM calls Panel API
           → Panel creates SSO account
           → Panel assigns tier + limits
           → Customer gets invite email
```

### Responsibility boundaries

| Concern | Owner |
|---|---|
| Identity / authentication | SSO |
| Account provisioning + tier assignment | Panel |
| Tier feature logic (what each tier unlocks) | inteteam-support |
| Usage counters | inteteam-support |
| Customer group definitions (within a tenant) | Business owner (configured in inteteam-support) |
| Company source of truth | CRM |

---

## 6. Tenant & Billing Model

### Two-layer billing

InteTeam charges business owners based on **resource consumption** (wholesale rate). Business owners charge their own customers however they choose. InteTeam does not need to know or care about the retail layer.

### Usage counters (instrument from Phase 1 — cheap now, expensive to retrofit)

| Metric | Billing signal |
|---|---|
| `tickets_created` | Core volume |
| `chat_sessions_started` | Higher value than tickets |
| `remote_session_minutes` | Most resource-heavy — charge per minute |
| `agents_registered` | Per-seat signal |
| `kb_lookups` | Cheapest — bulk volume |

Counters are stored per tenant per billing period. Panel reads them. Billing logic is a future concern — the counters just need to exist and be accurate from day one.

### Tenant plan limits (set by Panel per tenant)

Each tenant has configurable soft limits Panel can adjust without a deployment:

```
tickets_per_month: 100
chat_sessions_per_month: 50
remote_minutes_per_month: 300
agents_allowed: 5
```

When a tenant approaches a limit, they are notified. When exceeded, the feature is gated (configurable: hard block vs warning-only).

### Customer groups (tenant-configurable — not InteTeam-defined)

Each business owner can define their own customer tiers within inteteam-support. Example:

| Group | Channels available |
|---|---|
| Gold | Tickets + Chat + Remote |
| Standard | Tickets + Chat |
| Basic | Tickets only |

Group definitions are scoped per tenant. InteTeam does not define or enforce them — the business owner sets their own rules for their own customers. This is just a feature-access flag on each end-customer record.

---

## 7. Phases

### Phase 1 — Ticket System + Usage Foundation

**Goal:** Replace ad-hoc support with structured tickets. Instrument all usage counters from day one.

**User stories:**
- As a business owner's customer, I can raise a support ticket with my context pre-filled (company, app, page).
- As a business owner, I can view all tickets raised by my customers.
- As an InteTeam engineer, I can view tickets across all tenants and manage them.
- As a tenant, I can track ticket status (open, in progress, resolved).
- Ticket status changes trigger email notifications.

**Acceptance criteria:**
- [ ] SSO authentication — all users (tenants + engineers) authenticate via inteteam-sso
- [ ] Panel provisioning endpoint — Panel can create a tenant account and assign tier + limits via API
- [ ] Ticket created with: tenant_id, end_customer_id, app, page, category, description
- [ ] Ticket categories: Bug Report, Feature Request, How-To Question, Account Issue, Hardware Issue
- [ ] Status flow: open → in_progress → resolved → closed
- [ ] Internal notes visible only to InteTeam engineers
- [ ] Email notification on status change
- [ ] Engineer dashboard: filter by status, tenant, category, assignee
- [ ] Tenant portal: scoped to own company — sees tickets from their customers only
- [ ] Customer group model exists (even if group management UI is Phase 2)
- [ ] **All usage counters instrumented:** `tickets_created` increments on every ticket create
- [ ] Counter reset logic (monthly) in place from day one

---

### Phase 2 — Live Chat

**Goal:** Real-time text chat between a support agent and an end customer for quick issues.

**User stories:**
- As an end customer, I can start a live chat session.
- As an engineer or tenant agent, I see incoming chat requests in a queue and can accept/reject.
- If no agent is available, the chat is auto-converted to a ticket with the transcript attached.
- Chat history is permanently stored and linkable to tickets.

**Acceptance criteria:**
- [ ] WebSocket-based real-time messaging (Laravel Reverb)
- [ ] Agent availability toggle (online / away / offline)
- [ ] Chat queue — first agent to accept owns the session
- [ ] Auto-ticket fallback when no agent available within 60 seconds
- [ ] Full transcript stored, linkable to tickets
- [ ] Tenant can configure which customer groups have access to chat
- [ ] `chat_sessions_started` counter increments on session accept
- [ ] Typing indicator visible to customer

**Dependencies:** Phase 1 complete.

---

### Phase 3 — Remote Desktop

**Goal:** Opt-in remote assistance for hardware troubleshooting (printers, scanners, local network).

**User stories:**
- As an engineer on a hardware ticket, I can request a remote session with the customer.
- As an end customer, I can accept or decline a remote session request.
- The customer installs the Inte.Team desktop agent once; future sessions are one-click.

**Acceptance criteria:**
- [ ] Engineer can initiate a remote session from within a ticket
- [ ] Customer receives request notification (in-app + email) — explicit accept required
- [ ] Desktop agent downloadable from support portal (Windows 11 first)
- [ ] Agent is headless — no heavy UI for the customer
- [ ] Video stream rendered in engineer's browser via WebRTC
- [ ] Engineer can control mouse and keyboard on the remote machine
- [ ] Session logged: start time, end time, engineer, tenant, customer, ticket link
- [ ] `remote_session_minutes` counter increments per minute of active session
- [ ] `agents_registered` counter increments on first agent install per customer
- [ ] Tenant can restrict remote desktop to specific customer groups only
- [ ] Agent connects to `inteteam-remote` signaling server exclusively

**Infrastructure:** `inteteam-remote` on Dell R550 via NPM (`remote.inte.team`). UDP `50000–50100` forwarded directly for media. See `inteteam-remote` repo.

**Dependencies:** Phase 1 required (session logging). Phase 2 recommended.

---

### Phase 4 — Knowledge Base Surface (Ongoing)

Runs in parallel. Surfaces relevant manual articles inside ticket and chat flows. Content pipeline from `inteteam-support/docs/` → `inteteam_cms` → RAG (`inteteam-rag` on VM105). Not a new build — content operations work.

---

## 8. Tech Stack

| Layer | Technology | Notes |
|---|---|---|
| Framework | Laravel 12 | Consistent with CRM, Panel, oor-hq |
| Frontend | React 19 + Inertia + Tailwind | Same component library as CRM |
| Auth | inteteam-sso | All users (tenants + engineers); SSO-issued JWT |
| DB | PostgreSQL | Separate instance; consistent with Panel |
| Real-time (Phase 2) | Laravel Reverb | Self-hosted WebSockets |
| Remote signaling (Phase 3) | Go + Pion WebRTC | `inteteam-remote` repo |
| Remote agent (Phase 3) | Go — Windows first | Compiled binary, distributed via support portal |
| Email | Resend / SMTP per tenant | Follows CRM email provider pattern |

---

## 9. Repo Structure

```
InteTeam/inteteam-support      ← this repo (Laravel app, PRD, docs)
InteTeam/inteteam-remote       ← Go signaling server + desktop agent
```

---

## 10. Build Order

1. Scaffold Laravel 12 app (oor-hq as base)
2. Wire SSO auth
3. Add Panel provisioning API endpoint
4. Phase 1: Ticket system + counter foundation
5. Phase 2: Live chat (Reverb + queue)
6. Phase 3: Remote desktop (inteteam-remote + agent + stream UI)
7. Phase 4: Knowledge base surface layer

---

## 11. Decisions Made

| Decision | Answer |
|---|---|
| Auth | SSO throughout — all users (engineers, tenant admins, end customers) authenticate via inteteam-sso |
| Engineer accounts | SSO users with `inteteam_staff` role — no separate table. Role gates the engineer dashboard. |
| Port assignment | Panel auto-assigns on app creation, injects `PORT` into `.env`. Use `${PORT}` in docker-compose. |
| Customer groups | Tenant-defined inside inteteam-support — InteTeam does not define or enforce them |
| Retail billing | Not InteTeam's concern — permanently out of scope |
| Agent code signing | Phase 3 concern — deferred until agent build begins |

No open questions remain. PRD is ready for SOP Step 1 (scaffold).
