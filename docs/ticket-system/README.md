# Phase 3: Ticket System — Planning

## Overview

Support ticket system integrated into each InteTeam app. Provides structured bug reporting, feature requests, and support escalation when RAG chat can't resolve the issue.

**Status:** Planning — depends on Phase 1 (manual) and Phase 2 (RAG chat) being in place.

---

## Requirements

### For Tenants (shop users)

- "Report Problem" / "Get Help" button accessible from every page in every app
- Pre-filled context: app name, current page/route, company, user, browser
- Structured categories: bug report, feature request, how-to question, account issue
- Screenshot/attachment support
- Status tracking: open, in progress, resolved, closed
- Email notifications on status changes
- Chat history attached if escalated from RAG

### For InteTeam Team (internal)

- Unified dashboard across all apps
- Priority levels, assignment, SLA tracking
- Link tickets to specific apps/features
- Bulk operations (merge duplicates, batch close)
- Analytics: most common issues, resolution time, trending problems
- Identify documentation gaps (frequent questions = missing manual content)

---

## Architecture Options

### Option A: Built into inteteam_crm

- Add ticket tables to CRM database
- Internal dashboard at `/admin/support/tickets`
- Tenants submit via API from any app
- Pros: no new infrastructure, reuse existing auth
- Cons: CRM becomes even larger, tight coupling

### Option B: Standalone microservice

- Separate Laravel app (e.g. `inteteam-support-api`)
- Own database, own deployment
- Each app submits via API
- Dashboard either in Panel or standalone
- Pros: clean separation, scales independently
- Cons: more infrastructure to maintain

### Option C: Extend inteteam-panel

- Panel already manages infrastructure
- Add support module to Panel
- Tenants submit from their apps, team manages in Panel
- Pros: Panel is already the "admin" tool
- Cons: Panel uses PostgreSQL, different stack concerns

**Recommendation:** Start with Option A (built into CRM) for speed. Extract to standalone if/when it outgrows CRM.

---

## Integration with RAG Chat (Phase 2)

```
User asks question in chat
    -> RAG provides answer
    -> User clicks "This didn't help"
    -> Ticket creation form opens (pre-filled)
    -> Chat transcript attached to ticket
    -> Ticket created with category "RAG escalation"
    -> InteTeam team sees: question + RAG answer + user feedback
```

This feedback loop is valuable: every RAG escalation reveals a documentation gap or a real bug.

---

## Ticket Lifecycle

```
Created (by user or RAG escalation)
    -> Triaged (auto-categorized or manual)
    -> Assigned (to team member)
    -> In Progress
    -> Resolved (team marks done)
    -> Closed (auto after 7 days if no response, or user confirms)
```

---

## Data Model (Draft)

| Field | Type | Notes |
|-------|------|-------|
| id | ULID | Primary key |
| company_id | FK | Tenant isolation |
| user_id | FK | Who submitted |
| app | string | Which InteTeam app (crm, panel, cms, sso) |
| category | enum | bug, feature_request, question, account |
| priority | enum | low, medium, high, critical |
| subject | string | Short description |
| body | text | Full description |
| context | json | Auto-captured: route, browser, OS, app version |
| chat_history | json | If escalated from RAG |
| status | enum | open, triaged, assigned, in_progress, resolved, closed |
| assigned_to | FK nullable | InteTeam team member |
| resolved_at | timestamp | When marked resolved |
| attachments | relation | Screenshots, files |

---

## Next Steps

1. Finish Phase 1 (manual) and Phase 2 (RAG) first
2. Analyze RAG escalation patterns to understand ticket categories
3. Decide architecture option (A, B, or C)
4. Design ticket submission API (shared across all apps)
5. Build internal dashboard
