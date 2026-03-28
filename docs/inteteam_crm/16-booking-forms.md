# Booking Forms

Create embeddable forms for your website that let customers submit bookings directly into the CRM.

**Bookings -> Forms**, or navigate to `/admin/bookings/forms`

---

## What Are Booking Forms?

A booking form is a widget you embed on your website. When a customer fills it in and submits, a new booking is automatically created in your Incoming list with all their details.

---

## Viewing Your Forms

The forms index page shows all your booking forms with:

- **Form name** and status badge (active/inactive)
- **Description** of what the form is for
- **Preview link** — open the form in a new tab to see what customers see
- **Copy embed code** — copy the embed snippet for your website
- **Edit / Delete** actions

---

## Creating a Form

1. Click **Create Form**
2. Choose a **custom form** (built in CMS -> Forms) — this defines the fields
3. Choose a **location** — where the booking will be assigned
4. Give the form a name and description
5. Save

The system generates an embed code you can paste into your website:

```html
<script src="https://yourcrm.com/embed/booking.js" data-booking="form-uuid"></script>
```

---

## What Happens When a Customer Submits

1. Customer fills in the form on your website
2. A new booking is created in **Incoming** with:
   - Customer name, email, phone (from form fields)
   - All form field responses visible in the booking detail (Form Data section)
   - Location set to the one configured on the form
3. You'll see it appear in your Incoming bookings list

---

## Editing a Form

1. Click **Edit** on the form
2. Change the name, description, linked form, or location
3. Save

Changes apply immediately — the embed code stays the same.

---

## Custom Form Builder

The actual form fields are built in **CMS -> Forms**. That's where you design what questions customers answer (text fields, dropdowns, checkboxes, etc.). The booking form links to one of these CMS forms.

See [CMS Forms](91-cms-forms.md) for how to build custom forms.
