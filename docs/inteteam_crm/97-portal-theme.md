# Portal Theme Editor

Customise how your customer portal looks — colours, fonts, layout, and branding. Your customers will see these settings when they log into the portal.

**Admin only.** Navigate to **Settings → Portal Theme**, or go to `/admin/settings/portal-theme`.

---

## How It Works

The portal uses a theme system where you only change what you want — everything else uses sensible defaults. Your changes are saved as overrides, so new features we add later will automatically look right without you needing to update anything.

---

## Theme Sections

The editor is organised into six tabs:

### Colours

Set your brand's colour palette. Only **Primary**, **Secondary**, and **Accent** are required — the rest auto-generate if you leave them empty.

| Colour | Used For | Default |
|--------|----------|---------|
| **Primary** | Buttons, links, active states | Blue (#2563EB) |
| **Primary Hover** | Hover state for primary buttons (auto if empty) | Darker blue |
| **Secondary** | Secondary buttons, badges | Dark blue (#1E40AF) |
| **Accent** | Highlights, call-to-action elements | Amber (#F59E0B) |
| **Background** | Page background | White |
| **Surface** | Card backgrounds | Light grey |
| **Text** | Main text colour | Near-black |
| **Text Muted** | Secondary/helper text | Grey |
| **Text on Primary** | Text on primary-coloured backgrounds | White |
| **Border** | Borders and dividers | Light grey |
| **Success** | Completed bookings, success messages | Green |
| **Warning** | Pending actions, warnings | Amber |
| **Error** | Error messages, cancelled items | Red |

Click any colour swatch to open the picker, or type a hex code directly (e.g. `#D32F2F`).

### Brand

| Setting | Description |
|---------|-------------|
| **Display Name** | Override your company name on the portal (leave blank to use your company name) |
| **Tagline** | Short text shown below the logo on the login page |

> Logo and favicon upload will be available in a future update.

### Typography

| Setting | Description | Default |
|---------|-------------|---------|
| **Font Family** | The main font used across the portal | Inter |
| **Heading Font** | Optional separate font for headings | Same as body |
| **Base Size** | Root font size in pixels | 16px |
| **Line Height** | Spacing between lines of text | 1.5 |

### Layout

| Setting | Description | Default |
|---------|-------------|---------|
| **Border Radius** | How rounded corners are on cards and buttons (0 = square, 24 = very round) | 8px |
| **Large Radius** | Rounding for larger elements like modals | 12px |
| **Spacing Unit** | Base spacing between elements | 4px |
| **Max Width** | Maximum content width on wide screens | 1280px |

### Dark Mode

| Setting | Description |
|---------|-------------|
| **Enable dark mode** | Toggle on/off — when enabled, customers can switch between light and dark mode |
| **Dark colours** | Override the automatic dark mode colours if the defaults don't suit your brand |

### Content

| Setting | Description |
|---------|-------------|
| **Welcome Title** | Greeting on the dashboard (e.g. "Welcome back") |
| **Welcome Message** | Optional subtitle below the greeting |
| **Footer Text** | Custom footer text (use `{year}` and `{brand.name}` as placeholders) |
| **Powered by InteTeam** | Show or hide the "Powered by InteTeam" badge in the footer |

---

## Live Preview

On the right side of the editor, you'll see a **live preview** that updates as you change settings. It shows:

- A mini portal header with your primary colour
- A sample booking card with your surface and border colours
- A button in your primary colour
- Status indicator dots (success, warning, error)
- Footer with powered-by badge

This lets you see how changes look before saving.

---

## Saving Changes

1. Make your changes in any tab
2. Click **Save Theme** at the bottom
3. You'll see a confirmation: "The portal theme was updated."

Changes take effect immediately on the customer portal.

---

## Resetting to Defaults

If you want to start over:

1. Click **Reset to Defaults** at the top-right
2. Confirm when prompted
3. All your overrides are removed — the portal goes back to the default InteTeam theme

---

## Tips

- You only need to set the colours that differ from the defaults — leave everything else alone
- Test your colours in both light and dark mode to make sure they look good
- The preview updates instantly — use it to experiment before saving
- If something looks wrong, you can always reset to defaults
