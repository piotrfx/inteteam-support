# InteTeam Panel — User Manual Scope

## Overview

Hosting control panel for managing InteTeam infrastructure and customer onboarding. Used by InteTeam internal team.

**Status:** Deploying to `panel.inteteam.co.uk` on Dell R550.

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

## Notes

- Panel has strictest code standards (PHPStan level 9)
- Uses PostgreSQL (unlike CRM which uses MariaDB)
- Manual audience is more technical than CRM tenants
- Wildcard `*.panel.inteteam.co.uk` reserved for future customer subdomains + SSO
