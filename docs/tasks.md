# inteteam-support — Build Tasks

Read `docs/PRD.md` before starting any phase. Each phase must be fully complete and tested before the next begins.

---

## Phase 0 — Foundation

- [ ] Scaffold Laravel 12 + React 19 app (use oor-hq as template)
- [ ] Docker: PHP-FPM + Nginx + PostgreSQL + Redis + queue-worker, `docker-compose.yml` + `docker-compose.prod.yml`. Container prefix: `support_`
- [ ] SSO integration — all users authenticate via inteteam-sso (match CRM/oor-hq pattern); role `inteteam_staff` routes to engineer dashboard, `tenant_admin` routes to tenant portal, `end_customer` routes to customer view
- [ ] Panel provisioning API endpoint — Panel can POST to create a tenant, assign tier, set usage limits (tickets_per_month, chat_sessions_per_month, remote_minutes_per_month, agents_allowed)
- [ ] `tenants` migration + model — ULID PKs, plan limits JSON column, billing period tracking
- [ ] `customer_groups` migration + model — belongs to tenant; name + features JSON (which channels are enabled per group)
- [ ] `usage_counters` migration + model — tenant_id, metric (enum), period (YYYY-MM), count, last_reset_at; unique index on (tenant_id, metric, period)
- [ ] `UsageCounterService` — increment(tenant, metric), reset(tenant, period), isWithinLimit(tenant, metric); wire to tenant plan limits
- [ ] `install.sh` — must leave service fully running after single run; print all generated secrets with destinations; derive `DEPLOY_DIR` dynamically, never hardcode
- [ ] `deploy.sh` — standard 12-step; first-time only
- [ ] `post-deploy.sh` — called by Panel after every deploy: migrations, cache clear, queue restart, `append_if_missing` for all env keys
- [ ] Register app in Panel (auto-assigns port; use `${PORT}` in docker-compose host port)
- [ ] `CLAUDE.md` + `docs/DATABASE_CONVENTIONS.md` + `docs/WORKFLOW_ENFORCEMENT.md` in repo root

---

## Phase 1 — Ticket System

- [ ] Feature doc: `docs/features/tickets/README.md` (user stories, acceptance criteria)
- [ ] Architecture doc: `docs/features/tickets/architecture.md`
- [ ] Component inventory: `docs/features/tickets/COMPONENT_INVENTORY.md`
- [ ] `tickets` migration — ULID PK, tenant_id, end_customer_id, app, page, category (enum), description, status (enum: open/in_progress/resolved/closed), assigned_to, HasCompanyScope equivalent for tenant scoping
- [ ] `ticket_notes` migration — ULID PK, ticket_id, author_id, body, is_internal (bool)
- [ ] `Ticket` + `TicketNote` models with relationships and scopes
- [ ] `TicketPolicy` — tenant sees own tickets only; engineer sees all
- [ ] `TicketService` — create, assign, updateStatus, addNote
- [ ] Engineer: `EngineerTicketController` — index (all tenants, filterable), show, assign, updateStatus, addNote
- [ ] Tenant: `TenantTicketController` — index (own company), show, create
- [ ] Customer: `CustomerTicketController` — create, show own tickets
- [ ] Email notification on status change (queue job; Resend or SMTP per tenant setting)
- [ ] Pages: engineer dashboard, ticket detail (engineer view), tenant portal, ticket detail (tenant view), new ticket form
- [ ] CRM widget: "Open Support" button passes SSO token + app + page context to inteteam-support
- [ ] `tickets_created` counter increments in `TicketService::create`
- [ ] Feature gate: block ticket creation when tenant is at monthly limit (warning at 80%, hard block at 100%)
- [ ] Tests: guest redirected, wrong role → 403, ticket created (assertDatabaseHas), cross-tenant isolation → 404, status transitions, counter increments

---

## Phase 2 — Live Chat

- [ ] Feature doc + architecture doc + component inventory under `docs/features/chat/`
- [ ] Install + configure Laravel Reverb (self-hosted WebSockets)
- [ ] `chat_sessions` migration — ULID PK, tenant_id, end_customer_id, agent_id (nullable), status (enum: queued/active/closed/converted_to_ticket), ticket_id (nullable FK for escalation)
- [ ] `chat_messages` migration — ULID PK, session_id, author_id, body, sent_at
- [ ] `ChatSession` + `ChatMessage` models
- [ ] `ChatService` — startSession, acceptSession, sendMessage, closeSession, convertToTicket
- [ ] Agent availability: `agent_availability` table or Redis — toggle online/away/offline per engineer
- [ ] Chat queue broadcast event — all available engineers see incoming requests
- [ ] First-accept model — engineer accepts, session assigned; others removed from queue
- [ ] Auto-ticket fallback — if no acceptance within 60 seconds, `convertToTicket` fires with transcript attached
- [ ] Pages: chat queue (engineer), active chat window, customer chat widget
- [ ] Tenant configures which customer groups have chat access (via customer_groups features JSON)
- [ ] `chat_sessions_started` counter increments on session accept
- [ ] Feature gate: block new sessions when tenant is at monthly limit
- [ ] Tests: queue broadcast, auto-ticket fallback at 60s, cross-tenant isolation, counter increments, group access gate

---

## Phase 3 — Remote Desktop

### inteteam-remote (Go repo — `InteTeam/inteteam-remote`)

- [ ] Go module init (`go.mod`), add Pion WebRTC dependency
- [ ] `CLAUDE.md` + `README.md` in repo root
- [ ] Signaling server — WebSocket endpoint, session registry (session ID → peer pair), ICE candidate relay
- [ ] TURN relay — coturn or Pion TURN; restrict ICE to UDP `50000–50100`
- [ ] Session auth — engineer creates session token via inteteam-support API; agent presents token to signaling server to join
- [ ] Desktop agent (Windows 11) — screen capture via Desktop Duplication API, mouse/keyboard injection via SendInput, headless background service, connects to `remote.inte.team` signaling server
- [ ] Agent installer / silent install flag for distribution
- [ ] `docker-compose.yml` for signaling server + TURN; NPM proxy config for `remote.inte.team`
- [ ] `deploy.sh` for inteteam-remote on Dell R550

### inteteam-support integration

- [ ] Feature doc + architecture doc under `docs/features/remote/`
- [ ] `remote_sessions` migration — ULID PK, tenant_id, ticket_id (FK), engineer_id, customer_id, status (enum: requested/accepted/active/ended/declined), started_at, ended_at, duration_minutes
- [ ] `RemoteSession` model + `RemoteSessionService` — requestSession, acceptSession, endSession, logDuration
- [ ] Engineer: request session from ticket detail page; session token generated and sent to customer
- [ ] Customer: in-app + email notification with accept/decline; agent download link if not installed
- [ ] Stream UI — HTML5 canvas WebRTC consumer in engineer's browser, mouse/keyboard capture forwarded via DataChannel
- [ ] Agent download page — Windows installer; shows install instructions
- [ ] `remote_session_minutes` counter increments per minute via polling job during active session
- [ ] `agents_registered` counter increments on first agent install per customer (agent calls registration endpoint)
- [ ] Tenant can restrict remote to specific customer groups only
- [ ] Feature gate: block session requests when tenant is at monthly minutes limit
- [ ] Tests: session lifecycle, duration logging, counter increments, group access gate, cross-tenant isolation

---

## Phase 4 — Knowledge Base Surface

- [ ] Article suggestion in ticket creation flow — query `inteteam-rag` with ticket description, surface top 3 articles before submit ("Did you check this guide?")
- [ ] Article suggestion in chat — surface relevant articles when session starts
- [ ] `kb_lookups` counter increments on every RAG query from inteteam-support
- [ ] Tenant can toggle KB suggestions on/off per customer group
