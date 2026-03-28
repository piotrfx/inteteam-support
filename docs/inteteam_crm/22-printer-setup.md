# Printer Setup

Configure your label printers in the CRM so staff can print barcodes.

**Settings -> Hardware tab -> Printing**, or navigate to `/admin/settings/printing`

---

## Step 1: Add a Print Station

A station represents one location where a printer is connected (e.g. "Main Counter", "Back Room").

1. Go to **Settings -> Hardware -> Printing**
2. Click **Add Station**
3. Enter a name (e.g. "D&J Records - Main Counter")
4. Click **Create**
5. A **token** is shown (starts with `st_`) — **copy this immediately**, it's shown only once

This token is used by the Print Bridge software to authenticate. Keep it safe.

The station will show a **red dot** (offline) until the Print Bridge connects.

---

## Step 2: Add a Printer

1. On your station, click **Add Printer**
2. Fill in the details:

| Field | What to enter |
|-------|-------------|
| **Name** | A friendly name (e.g. "Zebra ZD230", "Brother P900W") |
| **Driver** | See table below |
| **Connection** | `lan` (network) or `usb` (direct USB cable) |
| **Address** | Printer's IP and port (e.g. `192.168.1.100:9100`) or USB path (e.g. `/dev/usb/lp0`) |
| **Default** | Toggle ON if this is your main printer |

### Choosing a Driver

| Driver | Use for | Notes |
|--------|---------|-------|
| **zebra_zpl** | Zebra printers (ZD230, ZD420, etc.) | Generates ZPL commands. Best for Zebra. Use LAN connection. |
| **brother_ql** | Brother P-touch (P900W, P750W) | Direct raster protocol. Works via USB or LAN. |
| **cups** | Any printer configured in CUPS (Linux) | Recommended for Brother on Linux — most reliable. |
| **raw_tcp** | Any network printer on port 9100 | Generic. Sends data as-is. |

### Finding Your Printer's IP Address

- **Zebra:** Check the printer's display menu, or print a network config label
- **Brother:** Press and hold the Wi-Fi button to print network info
- **Router:** Check your router's DHCP client list for the printer's name

3. Click **Save**

---

## Step 3: Install the Print Bridge

The Print Bridge is a small program that runs on the computer connected to the printer. It polls the CRM for print jobs and sends them to the printer.

See [Print Bridge Installation](23-print-bridge.md) for setup instructions.

---

## Step 4: Verify

1. The station should show a **green dot** (online) once the bridge is running
2. Go to any booking page and click **Print Label**
3. Check **Settings -> Hardware -> Printing -> View Jobs** to see the job status
4. Your printer should print the label

---

## Monitoring

### Station Status

- **Green dot** — bridge is connected and polling
- **Red dot** — bridge is offline (not running or can't reach CRM)
- **Last seen** timestamp shows when the bridge last checked in

### Job History

Click **View Jobs** to see all print jobs:

- **Pending** — waiting for bridge to pick up
- **Sent** — bridge received it, sending to printer
- **Completed** — printed successfully
- **Failed** — something went wrong (error message shown)
- **Cancelled** — cancelled by admin

You can cancel pending jobs from this page.

---

## Label Options

In the **Label Options** card:

- **Show ID on label** — when ON, prints human-readable text (e.g. "BK-001234") below the barcode. When OFF, prints barcode only.
