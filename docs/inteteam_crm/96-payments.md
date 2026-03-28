# Payments

View payment transactions and summaries.

**Business -> Payments -> Reports**, or navigate to `/admin/payments`

---

## Summary Cards

| Card | What it shows |
|------|-------------|
| **Collected** (green) | Total payments received |
| **Pending** (yellow) | Payments awaiting processing |
| **Refunded** (red) | Total refunds issued |
| **Total Transactions** | Transaction count with average amount |

---

## Transaction List

A table of all payment transactions:

| Column | What it shows |
|--------|-------------|
| **Date** | Transaction date |
| **Booking** | Linked booking reference (click to view) |
| **Customer** | Customer name |
| **Amount** | Payment amount in GBP |
| **Status** | Pending, Paid, Cancelled, Refunded, Partial Refund, or Failed |
| **Reference** | Payment gateway reference |

### Filtering

- **Date range** — From/To date filters
- **Status** — filter by payment status
- **Search** — find by customer or reference

---

## Payment Settings

Configure payment processors in **Settings -> Payments tab**:

| Processor | Settings page |
|-----------|-------------|
| **Dojo** | `/admin/settings/dojo` |
| **Stripe** | `/admin/settings/stripe` |
| **PayPal** | `/admin/settings/paypal` |
| **WorldPay** | `/admin/settings/worldpay` |

Each requires API credentials from the respective payment provider.
