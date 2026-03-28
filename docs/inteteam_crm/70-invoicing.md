# Invoicing

Create, send, and manage invoices for your customers.

**Business -> Invoices -> All Invoices**, or navigate to `/admin/invoices`

---

## Invoice List

A table showing all invoices:

| Column | What it shows |
|--------|-------------|
| **Invoice #** | Invoice number (click to view) |
| **Customer** | Customer name |
| **Status** | Draft (grey), Sent (blue), Paid (green), Overdue (red), Cancelled (grey), Voided (orange) |
| **Total** | Amount in GBP |
| **Date** | Created date |

Credit notes are marked with a **CN** badge.

Filter invoices by status using the filter buttons.

---

## Creating an Invoice

1. Click **Create Invoice**
2. Fill in the sections:

### Customer Details

| Field | Required | Notes |
|-------|----------|-------|
| **Customer Type** | Yes | Private or Business |
| **Contact Name** | Yes | Customer's name |
| **Email** | No | For sending the invoice |
| **Phone** | No | Contact number |

If **Business** is selected, additional fields appear:
- **Company Name** (required)
- **VAT Number** (optional)
- **Company Number** (optional)

Plus optional address fields (Address Line 1, Line 2, City, Postcode, Country).

### Invoice Details

| Field | Required | Default |
|-------|----------|---------|
| **Invoice Date** | Yes | Today |
| **Due Date** | Yes | 30 days from today |

### Line Items

1. Click **Add Item** to add a line
2. For each item, enter:
   - **Description** — what you're charging for
   - **Quantity** — number of units
   - **Unit Price** — price per unit in GBP
3. The **line total** calculates automatically
4. Add more items as needed
5. Remove items with the **delete** button (minimum 1 item required)

The **subtotal** updates as you add items.

### Notes

Optional free-text field for payment terms, special instructions, etc.

3. Click **Create Invoice**

---

## Invoice Lifecycle

```
Draft -> Sent -> Paid
              -> Overdue (auto, past due date)
              -> Cancelled
              -> Voided & Reissued
```

### Changing Status

On the invoice detail page, use the action buttons:

| From | Available Actions |
|------|------------------|
| **Draft** | Mark as Sent, Cancel |
| **Sent** | Mark as Paid, Cancel, Void & Reissue, Issue Credit Note |
| **Overdue** | Mark as Paid, Cancel, Void & Reissue, Issue Credit Note |

### Void & Reissue

If an invoice has errors after being sent:

1. Click **Void & Reissue** (orange button)
2. Optionally tick "Notify customer by email"
3. Confirm — the original is voided and a new draft is created with the same details
4. Edit the new draft and send

### Credit Notes

To issue a credit note (refund/adjustment):

1. Click **Issue Credit Note** (purple button)
2. Confirm
3. A credit note is created linked to the original invoice

---

## PDF & Email

On any invoice detail page:

- **Download PDF** — generates and downloads the invoice as a PDF
- **Send Email** — emails the invoice PDF to the customer's email address
- **Edit** — only available when the invoice is in Draft status

---

## Invoice Settings

**Business -> Invoices -> Settings**, or navigate to `/admin/invoices/settings`

Configure defaults that apply to all invoices:

| Setting | Description |
|---------|------------|
| **VAT Enabled** | Toggle VAT on/off |
| **VAT Rate** | Percentage (e.g. 20%) |
| **VAT Number** | Your VAT registration number |
| **Invoice Prefix** | Prefix for invoice numbers (e.g. "INV-") |
| **Due Days** | Default payment term in days |
| **Bank Details** | Bank name, sort code, account number, account name |
| **Payment Terms** | Default text shown on invoices |
| **Default Notes** | Default notes text |
