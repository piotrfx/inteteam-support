# InteTeam Support Platform

Standalone support platform for InteTeam customers and their customers. Replaces ad-hoc TeamViewer, email chains, and Slack with a structured, branded support workspace.

**Status:** Planning — PRD and task list complete, build not started.

---

## What this is

A **B2B2C product**: InteTeam sells support capability to business owners (repair shops, web dev clients). Those business owners use it to support their own customers. InteTeam charges wholesale based on resource usage; the business owner charges their customers however they choose.

This is **not a CRM module**. It is a standalone Laravel 12 + React 19 app deployed independently, integrated with the rest of the InteTeam platform via SSO and thin APIs.

---

## Phases

| Phase | Name | Status |
|---|---|---|
| 0 | Foundation (scaffold, SSO, Panel provisioning, usage counters) | Not started |
| 1 | Ticket System | Not started |
| 2 | Live Chat (Laravel Reverb) | Not started |
| 3 | Remote Desktop (WebRTC via `inteteam-remote`) | Not started |
| 4 | Knowledge Base surface layer | Ongoing (content exists in `docs/`) |

---

## Where to start

1. Read [`docs/PRD.md`](docs/PRD.md) — architecture, decisions made, tenant billing model, acceptance criteria per phase.
2. Read [`docs/tasks.md`](docs/tasks.md) — ordered checklist, one phase at a time.
3. Read [`docs/CONTEXT.md`](docs/CONTEXT.md) — team, infrastructure map, related repos, technical constraints.

Do Phase 0 first. Each phase builds on the previous. Do not skip ahead.

---

## Related repos

| Repo | Purpose |
|---|---|
| `InteTeam/inteteam-remote` | Go + Pion WebRTC signaling server + desktop agent (Phase 3) |
| `inteteam_crm` | Source of company/tenant truth; "Open Support" button points here |
| `inteteam_sso` | All auth flows for all InteTeam apps |
| `inteteam-panel` | Provisions tenant accounts, assigns tiers, assigns port |
| `inteteam-rag` | RAG service on VM105 — powers Phase 4 KB surface |
