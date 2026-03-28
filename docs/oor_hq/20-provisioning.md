# Provisioning VIP Accounts (Root Admin)

Root admins manage VIP infrastructure for all companies.

---

## Provisioning a New VIP Account

1. Go to **Root Admin -> VIP Management**
2. Select the company
3. Click **Provision VIP**
4. The system creates:
   - A VIP account record in the database
   - An LXC container on Proxmox with the company's slug
   - An NPM proxy route at `{slug}.vip.domain.com`
   - An IP allocation from the VIP pool (10.0.5.x range)

This runs as a background job — it may take a minute to complete.

---

## Managing LXC Containers

From the Root Admin VIP page, you can:

| Action | What it does |
|--------|-------------|
| **Start** | Boots the company's LXC container |
| **Stop** | Shuts down the container gracefully |
| **Rebuild** | Destroys and recreates the container from template |
| **Delete** | Removes the container, NPM route, and IP allocation |

---

## Deprovisioning

1. Click **Delete** on the company's VIP account
2. Confirm
3. The system removes:
   - The LXC container from Proxmox
   - The NPM proxy route
   - The IP allocation
   - The VIP account record

This is irreversible — all data inside the container is lost.

---

## Environment Requirements

The following must be configured in `.env`:

| Variable | Purpose |
|----------|---------|
| `PROXMOX_HOST` | Proxmox API URL (management LAN) |
| `PROXMOX_NODE` | Proxmox node name (e.g. "pve") |
| `PROXMOX_API_TOKEN_ID` | API token for authentication |
| `PROXMOX_API_TOKEN_SECRET` | API token secret |
| `NPM_HOST` | Nginx Proxy Manager URL |
| `NPM_EMAIL` | NPM admin email |
| `NPM_PASSWORD` | NPM admin password |
| `VIP_BASE_DOMAIN` | Base domain for VIP URLs (e.g. "vip.domain.com") |
