# Scanning Barcodes

Scan a barcode label on a device or item and jump straight to its page in the CRM. Works with phone cameras and physical USB/Bluetooth scanners.

---

## Camera Scanning (Phone or Desktop)

1. Tap the **barcode scanner icon** in the top-right of the navigation bar (it's always visible, on every page)
2. A camera dialog opens
3. Point your camera at the barcode label
4. The system reads the barcode and searches for a match
5. **If found:** you're taken directly to the booking, order, part, or inventory item page
6. **If not found:** a "Not found" message appears — you can scan again

### Tips

- On phones, the **rear camera** is used automatically
- On desktops/laptops, any available camera works
- The app needs **camera permission** — your browser will ask the first time
- Works in the **PWA** (installed app) on both Android and iOS
- Supported barcode types: CODE_128, EAN_13, CODE_39

---

## Physical USB/Bluetooth Scanner

If you have a USB barcode scanner plugged into your computer (or paired via Bluetooth):

1. Make sure the CRM is open in your browser
2. **Don't click anything** — just scan the barcode with the physical scanner
3. The scanner types the barcode value and presses Enter automatically
4. The CRM detects this and looks up the barcode
5. You're taken to the matching page

This works on **any page** in the CRM — you don't need to open a scanner dialog first.

### How It Works

Physical barcode scanners act as keyboards — they "type" the barcode characters very fast and press Enter. The CRM listens for this pattern and triggers a lookup automatically.

**Note:** The scanner won't trigger if you're typing in a text field (search box, form input, etc.). Click somewhere outside of text fields first.

---

## What Gets Matched

When you scan a barcode, the system searches in this order (first match wins):

| Priority | What it checks | Where it takes you |
|----------|---------------|-------------------|
| 1 | Booking reference | Booking detail page |
| 2 | Booking temp reference | Booking detail page |
| 3 | Storefront order reference | Order detail page |
| 4 | Part MPN | Part detail page |
| 5 | Inventory item SKU | Inventory item page |

If the barcode doesn't match any of these, you'll see a "No matching item found" message.
