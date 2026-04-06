# InteTeam Panel — User Manual Scope

## Overview

Hosting control panel for managing InteTeam infrastructure and customer onboarding. Used by InteTeam internal team.

**Status:** Live at `panel.inte.team` on Contabo 8GB (port 8000, behind NPM). Cold standby on Dell R550.

---

## Features to Document

| Feature | Status | Guide needed |
|---------|--------|-------------|
| Dashboard | Inherited from ubuntu-panel | Overview of managed services |
| Server management | Inherited | Adding/managing servers |
| Customer provisioning | Built | Creating customers, module lifecycle (provision/suspend/restore/deprovision) |
| Domain management | Built | DNS setup via Cloudflare or OVH per customer |
| Email provisioning (Mailu) | Built | Domain + info@ mailbox creation via Mailu API |
| Email provisioning (Resend) | Built | Resend domain verification + DNS record push |
| Customer domain & email card | Built | Manual trigger buttons for DNS, email DNS, Mailu, Resend setup |
| Customer edit | Built | Edit customer details (name, email, domain, DNS provider, email setup) |
| DNS verification | Built | Verify DNS: 5 checks (A, MX, SPF, DKIM, DMARC) with step labels |
| Mailu verification | Built | Verify Mailu: domain + mailbox existence check via API |
| Resend verification | Built | Verify Resend: domain status + per-record verification from Resend API |
| Welcome email | Built (untested) | Sends credentials + IMAP/SMTP details to customer. Blocked by staging TLS. |
| Resend Welcome button | Built | Re-sends welcome email using stored encrypted credentials |
| Onboarding guide | Built | Collapsible 7-step guide on /customers page |
| User access | Inherited | Panel user roles, 2FA setup |

---

## Customer Onboarding Flow (Admin)

1. **Add Customer** — name, email (personal), domain, DNS provider (OVH/Cloudflare), email setup (mailu/resend/both)
2. **Setup DNS** — enter target IP, click button → creates/updates A record
3. **Setup Mailu** — click button → creates domain + info@ mailbox in Mailu, stores encrypted credentials, sends welcome email to customer's personal email
4. **Generate DKIM** — in Mailu admin → domain detail → Generate keys → copy the p=... key
5. **Setup Email DNS** — paste DKIM key, click button → deletes old records, creates MX/SPF/DKIM/DMARC
6. **Verify DNS** — click button → all 5 checks green (A, MX, SPF, DKIM, DMARC)
7. **Verify Mailu** — click button → confirms domain + mailbox exist

The collapsible onboarding guide with these steps is also available on the `/customers` page itself.

## DNS Providers

- **Cloudflare** — default for new customers. Managed via API token.
- **OVH** — for existing customers with OVH-hosted domains. Managed via signed API (app key + secret + consumer key).

Both implement the same `DnsProvider` interface — the panel picks the right one per customer.

### What DNS Setup Creates

**Step 1 — Setup DNS** (A Record):
- Adds `A` record pointing domain to specified IP address
- Admin enters IP manually, or auto-fills from active module

**Step 2 — Setup Email DNS** (MX, SPF, DKIM, DMARC):
- Deletes ALL old records first (OVH defaults, legacy SPF)
- `MX` → `mail.ficner.co.uk` (Mailu shared host, priority 10)
- `TXT` (SPF) → `v=spf1 mx a:mail.ficner.co.uk ip4:62.31.81.187 ~all`
- `TXT` (DKIM) → `dkim._domainkey.{domain}` — paste public key from Mailu admin
- `TXT` (DMARC) → `v=DMARC1; p=reject; adkim=s; aspf=s`

**Step 3 — Setup Mailu** (domain + mailbox):
- Creates domain in shared Mailu instance via API
- Creates `info@{domain}` mailbox with generated password
- Stores credentials encrypted in Panel DB (admin can retrieve)
- Sends welcome email to customer's personal email with:
  - Temporary password + change instructions
  - Webmail URL
  - IMAP/POP3/SMTP connection details for email clients (Outlook, Thunderbird, Gmail app)
- "Resend Welcome" button available if email needs re-sending

**Step 4 — DKIM:** Generated in Mailu admin UI → domain detail → Generate keys. Paste the `p=...` value into the Setup Email DNS form. (Future: automated via Mailu fork API endpoint)

### Verification Endpoints

**DNS Verification** — "Verify DNS" button:
- 5 checks: A, MX, SPF, DKIM, DMARC — each with step number and action hints
- Full records table with type, name, content, TTL
- JSON: `GET /customers/{id}/dns-records`

**Mailu Verification** — "Verify Mailu" button:
- Checks domain + mailbox existence via Mailu API
- Shows webmail link
- JSON: `GET /customers/{id}/mailu-status`

**Resend Verification** — "Verify Resend" button:
- Domain verification status (pending/verified) from Resend API
- Per-record verification table
- JSON: `GET /customers/{id}/resend-status`

All JSON endpoints designed for PA/RAG agent task completion verification.

### Customers Onboarded (2026-04-04)

| Customer | Domain | Provider | Email | Status |
|----------|--------|----------|-------|--------|
| SAT SYSTEM BTC (Maciej) | satsystembtc.co.uk | OVH | info@satsystembtc.co.uk | All 5 green, Gmail delivery verified |
| Piotr Ficner (test) | inteteam.co.uk | Cloudflare | info@inteteam.co.uk | MX, SPF, DMARC green |
| Piotr Ficner (test) | ficner.info | OVH | info@ficner.info | All 5 green, Mailu mailbox created |

Full pipeline verified: Panel → Mailu (domain + mailbox) → DNS (MX, SPF, DKIM, DMARC) → Gmail delivery working.

### Pending (next session)

- **Welcome email delivery** — code built, blocked by staging Mailu `notls`. Test on Contabo VPS.
- **DKIM automation** — fork endpoint to expose public key via API (eliminates manual paste)
- **Force password change** — fork endpoint for first-login password reset
- **Contabo VPS deployment** — 207.180.220.29, dedicated IP, letsencrypt TLS

## Notes

- Panel has strictest code standards (PHPStan level 9)
- Uses PostgreSQL (unlike CRM which uses MariaDB)
- Manual audience is more technical than CRM tenants
- Wildcard `*.panel.inteteam.co.uk` reserved for future customer subdomains + SSO
