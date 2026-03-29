# InteTeam Print Bridge — User Manual Scope

## Overview

Setup and troubleshooting guide for the local print bridge agent. This is the most technical manual — targets shop admins or IT staff who install the bridge on shop PCs.

**Status:** Production — detailed README already exists in the print-bridge repo.

---

## Features to Document

| Feature | Guide needed |
|---------|-------------|
| Windows setup (.exe) | Download, configure .env, run, auto-start |
| Linux setup (systemd) | install/setup.sh, Docker alternative |
| Printer configuration | Finding printer IP, USB path, choosing driver (zebra_zpl, zing_raster, brother_ql, cups, raw_tcp) |
| Troubleshooting | Station offline, job stuck, USB/network errors |
| Updating | How to update to new versions |

---

## Content Source

The `inteteam-print-bridge/README.md` is already comprehensive. The user manual version should be a simplified, screenshot-heavy version of the same content, without the developer/architecture details.

## Target Audience

- Shop admin or IT person doing initial setup (one-time)
- Shop staff troubleshooting ("printer stopped working")
