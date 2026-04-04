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

1. Customer created via CRM webhook or manually in panel
2. Panel provisions LXC/VM, deploys Docker, configures NPM + DNS
3. Admin sets `dns_provider` (cloudflare or ovh) and `email_setup` (mailu, resend, or both)
4. Admin uses "Domain & Email" card buttons to trigger DNS and email setup
5. Resend domain verification runs automatically (5min polling, up to 10 retries)
6. Mailu mailbox provisioned when email module is enabled (planned)

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
- Pass/fail badges for A, MX, SPF, DMARC
- Full records table with type, name, content, TTL

Also available as JSON at `GET /customers/{id}/dns-records` — designed for PA/RAG agent task completion verification.

### First Customer Onboarded via OVH

SAT SYSTEM BTC (satsystembtc.co.uk) — Maciej's satellite & CCTV business. Successfully onboarded 2026-04-04:
- A record → 62.31.81.187 (Dell public IP)
- MX, SPF, DMARC records created
- All four DNS checks pass

## Notes

- Panel has strictest code standards (PHPStan level 9)
- Uses PostgreSQL (unlike CRM which uses MariaDB)
- Manual audience is more technical than CRM tenants
- Wildcard `*.panel.inteteam.co.uk` reserved for future customer subdomains + SSO
