# Printing Labels

Print barcode labels directly from the CRM. Labels can be stuck on devices, shelves, bins, or packaging for quick scanning later.

---

## Where to Print From

The **Print Label** button appears on these pages:

| Page | What's printed on the label |
|------|---------------------------|
| **Booking detail** (`/admin/bookings/{id}`) | Booking reference (e.g. INT-001/03-2026) |
| **Storefront order** (`/admin/storefront/orders/{id}`) | Order reference |
| **Part detail** (`/inventory/parts-stock/parts/{id}`) | Part MPN (manufacturer part number) |
| **Inventory item** (`/warehouse/inventory/{id}`) | Item SKU |

---

## How to Print

1. Open the booking, order, part, or inventory item page
2. Click the **Print Label** button (printer icon)
3. The CRM creates a print job and sends it to your default printer
4. Your label printer prints the barcode

That's it. The label is printed on your configured printer within a few seconds.

### Printing Multiple Copies

The default is 1 copy. If you need multiples, the Print Label button supports setting the number of copies (configurable in code — contact your admin if you need batch printing).

---

## Batch Printing (Label Batches)

Pre-print barcode labels in bulk for drop-off points and other uses.

**Settings -> Hardware tab -> Label Batches**, or navigate to `/admin/settings/label-batches`

### Creating a Batch

1. Click **Create Batch**
2. Enter a **prefix** (e.g. PETZONE), **range** (1–100), and **year**
3. Optionally add up to 3 lines of **custom text** (max 40 characters per line) — this appears below the barcode on every label in the batch
4. Click **Create Batch**

### Printing a Batch

1. Find the batch in the list and click **Print**
2. A confirmation dialog shows the batch name, label count, and estimated print time
3. Click **Start Printing** to begin

Labels print one at a time with short pauses between each. Larger batches take longer.

### Filtering Batches

Use the **search box** to filter by prefix, or the **status dropdown** to filter by created/printed/printing/failed.

---

## What's on the Label

- A **barcode** (CODE_128 format by default) encoding the reference/SKU/MPN
- Optionally, **human-readable text** below the barcode showing the value in plain text
- Optionally, the **company name** below the ID
- Optionally, up to **3 lines of custom text** (set per batch when creating label batches)

To configure what appears on labels:
1. Go to **Settings -> Hardware tab -> Printing**
2. Find the **Label Options** card
3. **Show ID on label** — toggle to show/hide the human-readable text (e.g. "BK-001234")
4. **Show company name** — toggle to show/hide the company name on the label. Useful when multiple businesses share a printer or stock area.

These apply to all labels printed by your company. Custom text is set per batch during batch creation.

---

## Requirements

Label printing requires:

1. A **print station** configured in your CRM settings (see [Printer Setup](22-printer-setup.md))
2. A **printer** added to that station (Zebra, Brother, or network printer)
3. The **Print Bridge** running on the computer connected to the printer (see [Print Bridge Installation](23-print-bridge.md))

If you don't have printing set up yet, clicking the Print Label button will show an error. Ask your admin to configure it first.
