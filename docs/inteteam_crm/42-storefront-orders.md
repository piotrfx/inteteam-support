# Storefront Orders & Enquiries

## Orders

**Business -> Storefront -> Orders**, or navigate to `/admin/storefront/orders`

### Order List

A table showing all customer orders:

| Column | What it shows |
|--------|-------------|
| **Reference** | Order reference (e.g. ORD-20260328-ABCDEF) |
| **Customer** | Name and email |
| **Total** | Order total in GBP |
| **Gateway** | Payment method badge (Stripe, Dojo, PayPal) |
| **Status** | Pending, Paid, Cancelled, Expired, or Refunded |
| **Date** | When the order was placed |

Click **View** to open the order detail page.

### Order Detail Page

- **Header:** Order reference, date placed, status badge, **Print Label** button
- **Customer card:** Name, email, phone
- **Payment card:** Gateway, transaction ID, payment timestamp
- **Invoice card:** Link to the associated invoice (if created)
- **Order Items table:** Each item with quantity, unit price, and subtotal
- **Totals:** Subtotal, VAT (if applicable), shipping (if applicable), total

---

## Enquiries

**Business -> Storefront -> Enquiries**, or navigate to `/admin/storefront/enquiries`

When your storefront is in **enquiry-only mode**, customers submit enquiries instead of placing orders.

### Enquiry List

| Column | What it shows |
|--------|-------------|
| **Reference** | Enquiry reference (e.g. ENQ-20260301-ABCDEF) |
| **Customer** | Name and email |
| **Items** | Number of products enquired about |
| **Status** | Pending, Contacted, Completed, or Cancelled |
| **Date** | When submitted |

Review each enquiry and follow up with the customer by phone or email.
