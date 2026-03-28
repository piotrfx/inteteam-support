# Warehouse Inventory

Track inventory items by SKU across warehouse locations, with full stock movement history.

**Business -> Warehouse -> Inventory**, or navigate to `/warehouse/inventory`

---

## Inventory List

A table showing all items across your warehouses:

| Column | What it shows |
|--------|-------------|
| **SKU** | Stock Keeping Unit — click to open detail page |
| **Name** | Item name |
| **Warehouse** | Which warehouse location |
| **Quantity** | Total units |
| **Available** | Units not reserved |
| **Status** | "In Stock" (green) or "Low Stock" (red) |

---

## Adding an Inventory Item

1. Click **+ Add Item**
2. Fill in the SKU, name, description, warehouse location, and initial quantity
3. Save

---

## Item Detail Page

Click any SKU to see its detail page:

**Left section:**
- **Details card** — SKU, description, warehouse location
- **Stock card** — total quantity, reserved, available, reorder level
- **Action buttons:**
  - **Add Stock** (green) — increase quantity
  - **Subtract Stock** (red) — decrease quantity
  - **Reserve** (blue) — reserve units for a job
  - **Transfer** (purple) — move to another warehouse
- **Edit** button — change item details
- **Print Label** button — print a barcode label with the SKU

**Right section:**
- **Stock Movement History** — a table showing every change:
  - Date, type (add/remove/reserve/transfer), quantity, who made the change, and reason

---

## Stock Actions

### Adding Stock
1. Click **Add Stock** on the item detail page
2. Enter the quantity to add and a reason
3. Confirm

### Subtracting Stock
1. Click **Subtract Stock**
2. Enter the quantity to remove and a reason
3. Confirm

### Reserving Stock
1. Click **Reserve**
2. Enter the quantity to reserve (for a specific job or booking)
3. Confirm

Reserved items show in the "Reserved" count and reduce the "Available" number.

### Transferring Stock
1. Click **Transfer**
2. Select the destination warehouse
3. Enter the quantity to transfer
4. Confirm

---

## Warehouse Locations

**Business -> Warehouse -> Locations**

Manage your warehouse locations — each location is a physical place where you store inventory.
