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
| **Address** | Printer's IP and port (e.g. `192.168.1.100:9100`), USB path (Linux: `/dev/usb/lp0`), or Windows printer share name (e.g. `ZDesigner ZD230-203dpi ZPL`) |
| **Default** | Toggle ON if this is your main printer |

### Choosing a Driver

| Driver | Use for | Notes |
|--------|---------|-------|
| **zebra_zpl** | Zebra printers (ZD230, ZD420, etc.) | Generates ZPL commands. Best for Zebra. Works via LAN or USB (Windows shared printer). |
| **zing_raster** | Zing / RONGTA thermal label printers | Converts images to ZPL raster graphics. Works via USB or LAN. |
| **brother_ql** | Brother P-touch (P900W, P750W) | Direct raster protocol. Works via USB or LAN. |
| **cups** | Any printer configured in CUPS (Linux) | Recommended for Brother on Linux — most reliable. |
| **raw_tcp** | Any network printer on port 9100 | Generic. Sends data as-is. |

### Finding Your Printer Address

**Network printers (LAN):**
- **Zebra:** Check the printer's display menu, or print a network config label
- **Brother:** Press and hold the Wi-Fi button to print network info
- **Router:** Check your router's DHCP client list for the printer's name

**USB printers on Windows:**
- Open PowerShell and run: `Get-Printer | Select-Object Name`
- Use the exact name shown (e.g. `ZDesigner ZD230-203dpi ZPL`)
- The printer must be **shared** — go to Printer properties > Sharing > tick "Share this printer"

**USB printers on Linux:**
- Run: `lpstat -p` to see CUPS printer names
- Or check USB device path: `ls /dev/usb/lp*`

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
- **Show company name** — when ON, prints the company name on the label below the ID. Useful when multiple businesses share a printer or stock area.

---

## Windows USB Quick Setup Checklist

For setting up a Zebra USB printer on a new Windows laptop:

1. [ ] Plug in the Zebra printer via USB — Windows should auto-install the driver
2. [ ] Open PowerShell, run `Get-Printer` to confirm the printer name
3. [ ] Right-click the printer in Settings > Printers > Properties > Sharing > **Share this printer**
4. [ ] In CRM: create a **new station** (Settings > Hardware > Printing > Add Station)
5. [ ] Copy the station **token** (shown once)
6. [ ] Add a **printer** to the station: Driver = Zebra ZPL, Connection = USB, Address = the exact printer name from step 2
7. [ ] Copy `inteteam-print-bridge.exe` to the laptop (e.g. `C:\PrintBridge\`)
8. [ ] Test from PowerShell:
   ```powershell
   .\inteteam-print-bridge.exe start --api-url https://crm.bookrepaironline.co.uk --token st_YOUR_TOKEN --interval 10000
   ```
9. [ ] Send a test print from the CRM — label should print
10. [ ] Set up **auto-start** (see below)

### Auto-Start on Windows Boot

So the bridge runs automatically when the laptop turns on:

> **Important:** Do NOT put the full command in a Windows shortcut target — it has a 260-character limit and will silently fail. Use a `.bat` file instead.

1. Create a file called `start-bridge.bat` in the bridge folder (e.g. `C:\inteteam_crm_print_bridge\start-bridge.bat`) with this content:
   ```bat
   @echo off
   cd /d "C:\inteteam_crm_print_bridge"
   inteteam-print-bridge.exe start --api-url https://crm.bookrepaironline.co.uk --token st_YOUR_TOKEN --interval 20000
   ```
   **The command must be on a single line** — do not split it across multiple lines.

2. Press `Win + R`, type `shell:startup`, press Enter
3. Copy `start-bridge.bat` into the Startup folder

That's it — Windows runs everything in the Startup folder on login.

### Verify Auto-Start

After reboot:
- The CRM station should show a **green dot** (online)
- Send a test print — it should work without manually starting anything
- If the green dot doesn't appear, open the `.bat` file manually to check for errors
