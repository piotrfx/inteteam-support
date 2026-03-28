# Printing Labels

Print barcode labels directly from the CRM. Labels can be stuck on devices, shelves, bins, or packaging for quick scanning later.

---

## Where to Print From

The **Print Label** button appears on these pages:

| Page | What's printed on the label |
|------|---------------------------|
| **Booking detail** (`/admin/bookings/{id}`) | Booking reference (e.g. BK-001234) |
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

## What's on the Label

- A **barcode** (CODE_128 format by default) encoding the reference/SKU/MPN
- Optionally, **human-readable text** below the barcode showing the value in plain text

To toggle the human-readable text on/off:
1. Go to **Settings -> Hardware tab -> Printing**
2. Find the **Label Options** card
3. Toggle **Show ID on label**

This applies to all labels printed by your company.

---

## Requirements

Label printing requires:

1. A **print station** configured in your CRM settings (see [Printer Setup](22-printer-setup.md))
2. A **printer** added to that station (Zebra, Brother, or network printer)
3. The **Print Bridge** running on the computer connected to the printer (see [Print Bridge Installation](23-print-bridge.md))

If you don't have printing set up yet, clicking the Print Label button will show an error. Ask your admin to configure it first.
