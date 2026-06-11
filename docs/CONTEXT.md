# Build Context

Everything a developer needs to understand before touching this codebase. Read this alongside `PRD.md` and `tasks.md`.

---

## Team

| Person | Role | Access |
|---|---|---|
| Piotr (piotrfx) | Lead / architecture | Full |
| Patryk | DevOps / infra | Shared infra responsibility |
| Lukasz | Developer | Occasional server access |

Git identity for this project: `git config user.name "piotrfx" && git config user.email "shopscot@gmail.com"`

---

## Infrastructure

### Servers

| Server | Spec | What runs there |
|---|---|---|
| Contabo 12GB VPS | 12GB RAM | CRM (prod), SSO (prod) |
| Contabo 8GB VPS | 8GB RAM | Mailu (email), Panel (prod), NPM (Nginx Proxy Manager) |
| Dell R550 (on-prem) | Staging / internal | oor-hq (staging), inteteam-remote (Phase 3 target), Dell WireGuard node |
| VM105 (192.168.0.42) | Internal VM | Ollama 8B, inteteam-rag, inteteam-cms; start-at-boot enabled |

### Networking

- All servers on WireGuard mesh (10.10.0.x range)
- NPM on 8GB Contabo proxies all public domains
- SMTP: Contabo **blocks port 587** — use **port 465 with SSL**. SMTP must originate from the WireGuard IP, not the VPS public IP.
- `remote.inte.team` → Dell R550 via NPM. UDP `50000–50100` forwarded directly for WebRTC media (Phase 3).

### Port assignment

Panel owns port assignment. When you register a new app in Panel, it auto-assigns a port and injects `PORT` into the app's `.env`. Always use `${PORT}` in `docker-compose.yml` host port bindings — never hardcode a port.

---

## Related repos and current state

### inteteam-remote (`InteTeam/inteteam-remote`)

Go + Pion WebRTC signaling server + desktop agent. Created 2026-06-10. Phase 3 of this project depends on it. The signaling server and TURN relay need to be built and deployed to the Dell R550 before Phase 3 of inteteam-support can begin.

### inteteam-rag (on VM105)

RAG service live at `rag.inte.team` (192.168.0.42:3100). 34+ CRM admin guides written, 131 chunks indexed. Phase 4 of this project queries this service to surface KB articles inside tickets and chat.

### inteteam_crm

Company/tenant source of truth. The "Open Support" button in CRM passes an SSO token + app + page context to inteteam-support. When a new company is created in CRM, it triggers Panel to provision a tenant account here (Path B provisioning — see PRD Section 5).

### inteteam_sso

All InteTeam apps authenticate through SSO. Follow the CRM → store_front SSO pattern exactly. Engineer accounts are SSO users with the `inteteam_staff` role — no separate table needed.

### inteteam-panel

Provisions tenant accounts via API. Assigns port. Sets plan limits (tickets_per_month, etc.). Panel reads usage counters from this app to drive future billing. The Panel provisioning endpoint (Phase 0) must exist before Panel can create tenants here.

---

## Technical constraints

### Deploy rules

- **Never re-run `deploy.sh` on production** after first deploy. It is first-time-only. Subsequent deploys go through `post-deploy.sh` via Panel.
- `post-deploy.sh` **must** start with `cd "$(dirname "${BASH_SOURCE[0]}")"` — Panel invokes it from a different working directory.
- `post-deploy.sh` handles: migrations, cache clear, queue restart, `append_if_missing` for all env keys. If an env key is missing between deploys, the key disappears. All keys must be covered.
- `install.sh` must leave the service fully running after a single run. Print all generated secrets with their destinations. Derive `DEPLOY_DIR` dynamically — never hardcode it.

### Docker

- Container prefix: `support_` (consistent with other apps: `crm_`, `panel_`, etc.)
- Alpine PHP-FPM: user is `www` (uid 1000). Storage directories must be `chown`ed from the host before first start.
- Dev machine uses APT Docker CE (not snap) — GPU compatibility requirement.

### Database

- PostgreSQL (consistent with Panel, oor-hq).
- Use ULID primary keys (consistent with CRM).
- Follow `docs/DATABASE_CONVENTIONS.md` once scaffolded.

### Frontend

- React 19 + Inertia + Tailwind. Same component library as CRM.
- shadcn RadioGroup is broken in the CRM — use native HTML radios if needed.

### Email

- Follows CRM email provider pattern: Resend or SMTP per tenant setting.
- Test on port 465/SSL only (Contabo blocks 587).

### Testing

- Cross-tenant isolation tests expect **404**, not 403 (matches `HasCompanyScope` behaviour in CRM).
- Eager loads with global scopes: use `withoutGlobalScopes()` when the parent model bypasses them.

---

## Playbook

All code changes follow the `inte-playbook` at `/Desktop/WebApps/inte-playbook/` (GitHub: `InteTeam/inte-playbook`).

- PHP/Laravel changes → `inte-playbook/laravel/`
- React/frontend → `inte-playbook/react/`
- Docker → `inte-playbook/docker/`
- Deployment → `inte-playbook/deployment/`

New CRUD resources: use `integen laravel:resource` and `integen react:page` from `inte-generator` before writing boilerplate manually. Generator CLI is at `/Desktop/WebApps/inte-generator/`.

---

## Build prerequisites (before Phase 0)

These must exist before scaffolding inteteam-support:

- [ ] inteteam-sso is deployed and accessible from the build server
- [ ] inteteam-panel can accept API calls to provision a new app
- [ ] Panel port registry has a free port assigned to inteteam-support (proposed: **8094** — verify in Panel first)
- [ ] DNS entry `support.inte.team` added in Cloudflare (inteteam.co.uk is on Cloudflare)
- [ ] NPM proxy rule created for `support.inte.team` → `localhost:${PORT}`

---

## Business context

InteTeam is early-stage, targeting 500+ repair shops. Budget is tight (~£70/mo total infra). Decisions should favour self-hosted, low-overhead solutions over managed services. The usage counter instrumentation in Phase 0 is cheap to build now and expensive to retrofit later — do not skip it.
