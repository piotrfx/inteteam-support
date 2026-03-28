# Company Setup

First-time setup for your company. You only need to do this once. All settings are at `/admin/settings`.

The Settings page has **9 tabs** along the top:

| Tab | What it configures |
|-----|-------------------|
| Scheduler | Locations, business hours, holidays |
| **Company** | Company details, email, theme |
| Messaging | Quick replies, WhatsApp, SMS |
| Payments | Dojo, Stripe, PayPal, WorldPay |
| Security | Two-factor authentication, notifications |
| Storefront | Online shop settings |
| Hardware | Print stations, label printers |
| Invoicing | Invoice defaults |
| Bookings | Task templates |

---

## Company Details

**Settings -> Company tab -> Company Details**

This is your business information. It appears in emails sent to customers.

| Field | What to enter | Example |
|-------|--------------|---------|
| **Business Name** | Already set (read-only) | D&J Records |
| **Address Line 1** | Your shop address | 123 High Street |
| **Town / City** | Your town | Edinburgh |
| **Postcode** | UK postcode format | EH1 1AA |
| **Phone Number** | UK format | 01234567890 or 07123456789 |
| **Show address in customer emails** | Tick to include your address in booking confirmation emails | |

Click **Save Details** when done.

---

## Email Settings

**Settings -> Company tab -> Email Settings**

Configure how the CRM sends emails to your customers.

### Resend API Setup

The CRM uses [Resend](https://resend.com) to send emails. You need an API key:

1. Create a free account at resend.com
2. Add and verify your domain (e.g. `yourshop.com`)
3. Copy your API key
4. Paste it into the **API Key** field
5. Enter your verified domain in **From Domain** (e.g. `yourshop.com`)
   - Emails will be sent from `bookings@yourshop.com`

### Email Template

Customise the booking confirmation message your customers receive:

1. Edit the **Message Content** text area (up to 2,000 characters)
2. Tick which details to include in emails:
   - Customer Name
   - Customer Email
   - Customer Phone
   - Booking Date/Time
   - Location
   - Services

### Test It

Click **Send Test Email** to send a test to your own email. Check your inbox to make sure it looks right, then click **Save Settings**.

---

## Theme & Appearance

**Settings -> Company tab -> Theme**

Customise the look and feel of your CRM:

- Choose your brand colours
- Toggle dark mode on/off
- Changes apply to all users in your company

---

## What to Configure First

For a new company, set up in this order:

1. **Company Details** — your address and phone number
2. **Email Settings** — so you can send booking confirmations
3. **Team Members** — invite your staff (see next guide)
4. **Printing** — if you have a label printer (Settings -> Hardware tab)
5. **Payments** — if you take payments through the CRM
6. **Storefront** — if you sell products online
