# Print Bridge Installation

The Print Bridge is software that runs on your shop PC (the one connected to the printer). It fetches print jobs from the CRM and sends them to your printer.

---

## Before You Start

You need:
- A **print station** created in CRM settings with the **token** (see [Printer Setup](22-printer-setup.md))
- A **printer** added to that station with the correct driver and address
- The printer **powered on** and connected (USB or network)

---

## Windows Setup (Recommended)

### Option A: Standalone .exe (easiest — no installation needed)

1. Download the `inteteam-print-bridge-win64.zip` from your admin
2. Unzip to any folder (e.g. `C:\PrintBridge\`)
3. Rename `.env.example` to `.env`
4. Open `.env` in Notepad and set:
   ```
   INTETEAM_API_URL=https://your-crm-url.com
   INTETEAM_STATION_TOKEN=st_your_token_here
   ```
5. Double-click `inteteam-print-bridge.exe`
6. A console window opens showing "Polling..." — this means it's working

**To auto-start on boot:**
- Press `Win+R`, type `shell:startup`, press Enter
- Create a shortcut to the `.exe` in this folder

### Option B: With Node.js

If you have Node.js 20+ installed:

```powershell
npm install
copy .env.example .env
# Edit .env with your CRM URL and token
npx tsx bin/bridge.ts start
```

**Note:** For USB printers on Windows, use the printer's network IP instead of USB path. Find the IP in the printer settings or your router's DHCP table.

---

## Linux Setup

### Option A: One-command installer (recommended)

```bash
sudo bash install/setup.sh
```

This installs to `/opt/inteteam-print-bridge`, prompts for your CRM URL and station token, and creates a systemd service that auto-starts on boot.

### Option B: Docker

```bash
cp .env.example .env
# Edit .env with your CRM URL and token
docker compose up -d
```

For USB printers, edit `docker-compose.yml` to match your USB device path (e.g. `/dev/usb/lp3`).

### Option C: Manual

```bash
npm install
cp .env.example .env
# Edit .env
npx tsx bin/bridge.ts start
```

---

## Verifying It Works

1. Start the bridge — you should see "Polling..." messages in the console
2. In the CRM, your station should show a **green dot** (online)
3. Go to a booking page and click **Print Label**
4. Check the bridge console — it should show the job being processed
5. Your printer should print the label

---

## Troubleshooting

### Bridge shows "Poll failed"
- Check your CRM URL in `.env` is correct and reachable
- Check the station token matches what's in the CRM
- Check internet connectivity

### Station shows red dot (offline) in CRM
- The bridge isn't running
- Check the console for error messages
- Make sure the `.env` file has the correct URL and token

### Job stuck on "pending"
- Bridge isn't running or isn't polling
- Check the token matches the station

### Print fails with "USB write failed"
- Printer is off or disconnected
- Check the USB cable
- On Linux, check device path: `ls /dev/usb/lp*`
- Check permissions: add your user to the `lp` group

### Print fails with "TCP timeout"
- Printer is off or IP changed
- Check the printer's IP on its display or router DHCP
- Test connectivity: try pinging the printer IP

### Labels print but look wrong
- Check the printer driver is correct (Zebra needs `zebra_zpl`, Brother needs `cups` or `brother_ql`)
- Check label size matches your printer (ZPL is set for 50x25mm at 203 DPI)
