# Parts Stock Catalog

Manage your parts inventory with categories, manufacturers, specifications, and stock levels.

**Business -> Parts & Stock -> Part Catalog**, or navigate to `/inventory/parts-stock/catalog`

---

## Catalog Overview

The catalog page has two sections:

**Left side — Category tree:**
- Hierarchical list of part categories
- Each shows a count of parts inside
- Click a category to see its parts
- Inactive categories are marked with a badge

**Right side — Quick actions:**
- **Browse All Parts** — view the full parts list
- **Create New Part** — add a new part

---

## Creating a Category

1. Click **Add Category** on the catalog page
2. Enter:
   - **Name** — category name (e.g. "Screens", "Batteries", "Logic Boards")
   - **Parent Category** — optional, to create sub-categories
   - **Description** — optional
3. Save

Use the **up/down arrows** to reorder categories.

---

## Adding a Part

1. Click **Create New Part**
2. Fill in:

| Field | Required | Description |
|-------|----------|------------|
| **MPN** | Yes | Manufacturer Part Number — the unique identifier |
| **Name** | Yes | Part name |
| **Manufacturer** | No | Who makes it |
| **Category** | No | Which category it belongs to |
| **Description** | No | Full description |
| **Specifications** | No | Key-value pairs for technical specs |
| **Active** | Yes | Toggle ON to make available |

3. Save

---

## Part Detail Page

Click any part to see its detail page:

- **Header:** MPN (monospace), active/inactive badge, name, manufacturer
- **Print Label button** — prints a barcode label with the MPN
- **Description** — full text
- **Technical Specifications** — two-column grid of spec key-value pairs
- **Stock Overview** — total stock and available stock across all warehouses
- **Category** — breadcrumb showing the category path
- **History** — created and last updated dates
- **Quick Actions** — links to manage stock and view all stock levels

---

## Suppliers

**Business -> Parts & Stock -> Suppliers**

Manage your parts suppliers — where you order parts from.

---

## Stock Levels

**Business -> Parts & Stock -> Stock Levels**

View stock across all warehouses for each part.

---

## Purchase Receipts

**Business -> Parts & Stock -> Receipts**

Record when parts arrive from suppliers.
