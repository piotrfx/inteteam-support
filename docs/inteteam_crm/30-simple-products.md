# Simple Products

Manage your products for the online storefront. Add items with prices, stock levels, and photos.

**Business -> Storefront -> Products**, or navigate to `/admin/inventory/products`

---

## Product List

A table showing all your products:

| Column | What it shows |
|--------|-------------|
| **Product** | Product name |
| **Price** | Sell price in GBP |
| **Stock** | Current stock quantity |
| **Listed** | Whether the item is visible in your online store |
| **Actions** | Edit and delete buttons |

Use the **search box** at the top to find products by name.

---

## Adding a Product

1. Click **+ Add Product**
2. Fill in the form:

| Field | Required | Description |
|-------|----------|------------|
| **Name** | Yes | Product name |
| **Category** | No | Choose from your category tree |
| **Price** | Yes | Sell price in GBP |
| **Initial Quantity** | Yes | Starting stock level |
| **Description** | No | Product description |
| **List for sale** | No | Toggle ON to make visible in online store |
| **Photo 1** | No | Primary image (JPEG, PNG, WEBP, or GIF, max 5MB) |
| **Photo 2** | No | Secondary image |

3. Click **Save**

---

## Editing a Product

1. Click the **Edit** button on any product row
2. Change any fields (same as the create form)
3. To change a photo: click **Replace** and upload a new one
4. To remove a photo: click **Remove** (shows "Will be removed on save" — click Undo if you change your mind)
5. Click **Save Changes**

---

## Stock Adjustments

To adjust stock on a product:

1. Go to the product edit page
2. Change the quantity
3. Save

For more detailed stock tracking with movement history, use the **Warehouse Inventory** (see [Warehouse Inventory](32-warehouse-inventory.md)).

---

## Deleting a Product

1. Click the **Delete** button on the product row
2. Confirm the deletion

Deleted products are removed from your storefront immediately.

---

## Exporting Products

Click the **Export** button at the top of the products page to download a CSV file of all your products. The CSV includes: name, MPN, category, manufacturer, description, sell price, quantity, and storefront listing status.

Use the exported file as a template for bulk imports — add more rows in the same format and re-import.

---

## Importing Products

Click the **Import** button to open the import wizard.

### Step 1: Upload

Upload a CSV or Excel file (max 10MB). You can use your own spreadsheet format — you don't have to match our columns exactly.

**Tip:** Create one product manually first, then Export it. Use that CSV as your template.

### Step 2: Map columns

Match your spreadsheet columns to product fields. The system auto-suggests mappings for common column names (e.g. "Title" → Name, "Selling Price" → Sell Price).

- **Name** and **Sell Price** must be mapped (required)
- Columns mapped to "Save as product spec" are stored as extra product data
- Columns mapped to "Skip" are ignored
- Set a **default quantity** for rows without a quantity column

### Step 3: Review

Before importing, you'll see:
- A preview of the first 10 rows
- Count of new products, duplicates, and errors
- Any new categories that will be created automatically
- For duplicates: choose to **skip** them or **update** existing products

### Step 4: Import

- Small imports (under 50 products) process instantly
- Large imports run in the background — a progress bar shows completion
- When done, you'll see a summary: how many were created, updated, skipped, and any errors
