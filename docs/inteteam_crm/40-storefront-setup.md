# Storefront Setup

Set up your online storefront to sell products and take enquiries from customers.

**Business -> Storefront -> Tutorial**, or navigate to `/admin/storefront/tutorial`

---

## Getting Started

The Tutorial page walks you through 6 steps. Follow them in order:

### Step 1: Enable the Storefront

1. Go to **Business -> Storefront -> Settings** (or `/admin/settings/storefront`)
2. Enable the public storefront
3. Set your brand colours
4. Choose checkout mode:
   - **Cart mode** — customers add items to a cart and check out with payment
   - **Enquiry only** — customers submit an enquiry about products (no payment)
5. Copy your **API key** if you plan to embed the storefront on an external website

### Step 2: Create Categories

1. Go to **Business -> Parts & Stock -> Part Catalog**
2. Create categories to organise your products (e.g. "Accessories", "Cables", "Parts")
3. Keep names short and clear
4. You can nest categories (sub-categories)
5. Toggle inactive to hide a category temporarily

### Step 3: Add Products

1. Go to **Business -> Storefront -> Products** (or `/admin/inventory/products`)
2. Click **+ Add Product**
3. Set the name, category, price, stock, and photos
4. Toggle **List for sale** to make it visible in the storefront
5. See [Simple Products](30-simple-products.md) for full details

### Step 4: Manage Stock Levels

- Use the **Adjust Stock** button on each product to add or remove units
- When available stock reaches 0, the product shows as "Out of Stock"
- Reserved quantities are tracked separately (useful for repair jobs)

### Step 5: Handle Enquiries

- When customers submit enquiries, they appear at **Business -> Storefront -> Enquiries**
- You'll get email notifications for new enquiries
- Use the reference number when replying to customers

### Step 6: Embed on Your Website (Optional)

If you have an external website, you can use the storefront API to display products:

- The API provides JSON endpoints for categories, products, and availability
- No API key needed for read-only endpoints
- Use `/enquiry` endpoint with POST to submit enquiries from your site
