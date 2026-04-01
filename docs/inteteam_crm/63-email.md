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
| **Drop-off receipt** | Confirmation to customer when item received at a drop-off point (opt-in, custom message) |
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

---

## Drop-Off Receipt Email

If you use drop-off points, you can send customers a confirmation email when their item is received.

1. Go to **Settings -> Company tab -> Email Settings**
2. Scroll down to **Drop-Off Notifications**
3. Tick **Send confirmation email to customer on drop-off**
4. Write your custom message (e.g. "We confirm your booking", "Your item is with us")
5. Save

The email automatically includes your company name, the booking reference, and the drop-off location. Click the **?** icon next to the section title for more details.

This is off by default — enable it only if you want customers to receive this email.
