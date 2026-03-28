# Email Notifications

The CRM sends emails to customers for booking confirmations, invoices, and notifications. Emails are sent via [Resend](https://resend.com).

---

## Setup

See [Company Setup - Email Settings](02-company-setup.md) for how to configure your Resend API key, from domain, and email template.

---

## What Gets Emailed

| Trigger | What's sent |
|---------|------------|
| **Booking confirmation** | Confirmation email with booking details (configurable content) |
| **Invoice sent** | Invoice PDF attached |
| **Password reset** | Reset link |
| **Team invitation** | Invitation link to join your company |

---

## Customising Email Content

1. Go to **Settings -> Company tab -> Email Settings**
2. Edit the **Message Content** text area
3. Toggle which details to include (name, email, phone, date, location, services)
4. Click **Send Test Email** to preview
5. Save
