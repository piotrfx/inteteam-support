# InteTeam Panel — User Manual Scope

## Overview

Hosting control panel for managing InteTeam infrastructure and customer onboarding. Used by InteTeam internal team.

**Status:** Live at `panel.inteteam.co.uk` on Dell R550 (port 8000, behind NPM).

---

## Features to Document

| Feature | Status | Guide needed |
|---------|--------|-------------|
| Dashboard | Inherited from ubuntu-panel | Overview of managed services |
| Server management | Inherited | Adding/managing servers |
| Customer provisioning | Built | Creating customers, module lifecycle (provision/suspend/restore/deprovision) |
| Domain management | Built | DNS setup via Cloudflare or OVH per customer |
| Email provisioning | Built (Resend), Planned (Mailu) | Resend domain verification, Mailu mailbox creation |
| Customer domain & email card | Built | Manual trigger buttons for DNS, email DNS, Resend setup |
| Customer edit | Built | Edit customer details (name, email, domain, DNS provider, email setup) |
| DNS verification | Built | Verify DNS button fetches live records from provider, shows pass/fail for A/MX/SPF/DMARC |
| User access | Inherited | Panel user roles, 2FA setup |

---

## Customer Onboarding Flow (Admin)

1. Create customer (manually or via CRM webhook) — set name, email, domain, DNS provider, email setup
2. **Step 1 — Setup DNS:** Enter target IP in the "A Record IP" field, click "Setup DNS" → creates A record
3. **Step 2 — Setup Email DNS:** Click "Setup Email DNS" → creates MX, SPF, DMARC records
4. **Verify:** Click "Verify DNS" → confirms all records with green/red badges and action hints for any failures
5. (Optional) Setup Resend for transactional email — runs automatically (5min polling, up to 10 retries)
6. (Planned) Mailu mailbox provisioned when email module is enabled

## DNS Providers

- **Cloudflare** — default for new customers. Managed via API token.
- **OVH** — for existing customers with OVH-hosted domains. Managed via signed API (app key + secret + consumer key).

Both implement the same `DnsProvider` interface — the panel picks the right one per customer.

### What DNS Setup Creates

**Setup DNS** (A Record):
- Adds `A` record pointing domain to specified IP address
- Admin enters IP manually, or auto-fills from active module

**Setup Email DNS** (MX, SPF, DMARC):
- `MX` → `email.inteteam.co.uk.` (Mailu shared host)
- `TXT` (SPF) → `v=spf1 mx a:email.inteteam.co.uk ~all`
- `TXT` (DMARC) → `v=DMARC1; p=quarantine; rua=mailto:postmaster@{domain}`
- DKIM added separately when Mailu is configured

### DNS Verification

"Verify DNS" button on customer detail page fetches live records from the provider and shows:
- Pass/fail badges for A, MX, SPF, DMARC — each with step number
- Action hints on failing checks (e.g. "← Use Setup DNS with target IP above")
- "All DNS records verified" message when everything passes
- Full records table with type, name, content, TTL

Also available as JSON at `GET /customers/{id}/dns-records` — designed for PA/RAG agent task completion verification.

### Customers Onboarded (2026-04-04)

| Customer | Domain | Provider | Status |
|----------|--------|----------|--------|
| SAT SYSTEM BTC (Maciej) | satsystembtc.co.uk | OVH | All 4 checks green (A, MX, SPF, DMARC) |
| Piotr Ficner (test) | inteteam.co.uk | Cloudflare | MX, SPF, DMARC green (A record not needed) |

Both DNS providers verified working end-to-end.

## Notes

- Panel has strictest code standards (PHPStan level 9)
- Uses PostgreSQL (unlike CRM which uses MariaDB)
- Manual audience is more technical than CRM tenants
- Wildcard `*.panel.inteteam.co.uk` reserved for future customer subdomains + SSO
