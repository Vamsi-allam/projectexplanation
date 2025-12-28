# Restaurant Billing System - HTML Documentation

## Overview
The `index.html` file is the main structure of the Restaurant Billing System. It defines the layout, modals, and all the UI elements that users interact with.

---

## Recent Notes (Dec 2025)

### Required IDs for search + rendering
The JavaScript expects these elements/IDs to exist so filtering and rendering works correctly:

**Tables**
- `id="tableSearch"` (table search input)
- `id="tableStatusFilter"` (available/occupied filter)
- `id="tablesGrid"` (container where table cards are rendered)

**Menu**
- `id="menuSearch"` (menu search input)
- `id="menuItems"` (container where menu cards are rendered)

If any of these IDs are renamed or removed, the corresponding search/filter/rendering will not work as expected.

---

## Document Structure

### 1. HEAD Section

```html
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Restaurant Billing System - ZeMoSo Labs</title>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="stylesheet" href="styles.css">
</head>
```

**Explanation:**
- `charset="UTF-8"` - Enables support for special characters like ₹ (Rupee symbol)
- `viewport` meta tag - Makes the page responsive on mobile devices
- **Google Fonts (Poppins)** - A modern, clean font used throughout the app
- **Font Awesome 6.4.0** - Icon library for all the icons (utensils, cart, plus, minus, etc.)
- **styles.css** - Our custom CSS file for styling

---

### 2. Header Section

```html
<header class="header">
    <div class="logo">
        <i class="fas fa-utensils"></i>
        <span>Hyderabad's Finest</span>
    </div>
    <div class="header-actions">
        <span class="current-time" id="currentTime"></span>
    </div>
</header>
```

**Explanation:**
- **Logo** - Displays the restaurant name "Hyderabad's Finest" with a utensils icon
- **Current Time** - Shows the current date and time (updated every second by JavaScript)
- The `id="currentTime"` is used by JavaScript to update the time dynamically

---

### 3. Main Content - Two Column Layout

```html
<main class="main-content">
    <section class="tables-section">...</section>
    <section class="menu-section">...</section>
</main>
```

**Explanation:**
- Uses CSS Grid to create a two-column layout
- **Tables Section** (Left) - Shows all 12 restaurant tables
- **Menu Section** (Right) - Shows all menu items that can be dragged to tables

---

### 4. Tables Section

```html
<section class="tables-section">
    <div class="section-header">
        <h2><i class="fas fa-chair"></i> Tables</h2>
    </div>
    <div class="filter-container">
        <div class="search-box">
            <i class="fas fa-search"></i>
            <input type="text" id="tableSearch" placeholder="Search table">
        </div>
    </div>
    <div class="tables-grid" id="tablesGrid"></div>
</section>
```

**Explanation:**
- **Section Header** - Title "Tables" with a chair icon
- **Search Box** - Allows filtering tables by name (e.g., "Table 1")
- **Tables Grid** (`id="tablesGrid"`) - Empty container where JavaScript will render all table cards

**Why is tablesGrid empty?**
The table cards are generated dynamically by JavaScript from the `tablesData` array. This allows:
- Dynamic updates when orders change
- Easy addition/removal of tables
- Real-time status updates (Available/Occupied)

---

### 5. Menu Section

```html
<section class="menu-section">
    <div class="section-header">
        <h2><i class="fas fa-book-open"></i> Menu</h2>
    </div>
    <div class="filter-container">
        <div class="search-box">
            <i class="fas fa-search"></i>
            <input type="text" id="menuSearch" placeholder="Search menu by item or course">
        </div>
    </div>
    <div class="menu-items" id="menuItems"></div>
</section>
```

**Explanation:**
- **Section Header** - Title "Menu" with a book icon
- **Search Box** - Allows searching menu items by name or category
- **Menu Items** (`id="menuItems"`) - Empty container where JavaScript renders all menu items

**Why is menuItems empty?**
Menu items are generated from `menuData` array in JavaScript. Benefits:
- Items are draggable (drag & drop to add to tables)
- Can be filtered and sorted dynamically
- Easy to add/remove menu items

---

### 6. Order Modal (Popup)

```html
<div class="modal-overlay" id="orderModal">
    <div class="modal">
        <div class="modal-header">
            <h3 id="modalTableName">Table 1 - Order Details</h3>
            <button class="close-modal" id="closeModal">
                <i class="fas fa-times"></i>
            </button>
        </div>
        <div class="modal-body">
            <div class="order-items" id="orderItems"></div>
            <div class="order-empty" id="orderEmpty">
                <i class="fas fa-shopping-cart"></i>
                <p>No items in this order yet</p>
            </div>
        </div>
        <div class="modal-footer">
            <div class="order-summary">
                <div class="summary-row">
                    <span>Subtotal:</span>
                    <span id="subtotal">₹0.00</span>
                </div>
                <div class="summary-row">
                    <span>Tax (5%):</span>
                    <span id="tax">₹0.00</span>
                </div>
                <div class="summary-row total">
                    <span>Total:</span>
                    <span id="grandTotal">₹0.00</span>
                </div>
            </div>
            <div class="modal-actions">
                <button class="btn btn-secondary" id="clearOrderBtn">
                    <i class="fas fa-trash"></i> Clear Order
                </button>
                <button class="btn btn-success" id="generateBillBtn">
                    <i class="fas fa-receipt"></i> Generate Bill
                </button>
            </div>
        </div>
    </div>
</div>
```

**Explanation:**

| Element | Purpose |
|---------|---------|
| `modal-overlay` | Dark background that covers the page when modal is open |
| `modalTableName` | Shows which table's order is being viewed |
| `closeModal` | X button to close the popup |
| `orderItems` | Container for list of ordered items (generated by JS) |
| `orderEmpty` | Shown when table has no orders |
| `subtotal` | Sum of all items before tax |
| `tax` | 5% of subtotal |
| `grandTotal` | Subtotal + Tax |
| `clearOrderBtn` | Removes all items from the table |
| `generateBillBtn` | Creates the final bill for printing |

---

### 7. Bill Modal (Popup)

```html
<div class="modal-overlay" id="billModal">
    <div class="modal bill-modal">
        <div class="modal-header">
            <h3><i class="fas fa-receipt"></i> Bill</h3>
            <button class="close-modal" id="closeBillModal">
                <i class="fas fa-times"></i>
            </button>
        </div>
        <div class="modal-body">
            <div class="bill-content" id="billContent"></div>
        </div>
        <div class="modal-footer">
            <div class="modal-actions">
                <button class="btn btn-primary" id="printBillBtn">
                    <i class="fas fa-print"></i> Print Bill
                </button>
                <button class="btn btn-success" id="endSessionBtn">
                    <i class="fas fa-check"></i> End Session
                </button>
            </div>
        </div>
    </div>
</div>
```

**Explanation:**
- **billContent** - Empty div where JavaScript generates the formatted bill
- **printBillBtn** - Triggers browser's print dialog
- **endSessionBtn** - Clears the table's order and marks it as available

---

### 8. Toast Notification

```html
<div class="toast" id="toast">
    <i class="fas fa-check-circle"></i>
    <span id="toastMessage">Item added successfully!</span>
</div>
```

**Explanation:**
- A small notification that appears at the bottom of the screen
- Shows feedback messages like "Item added to Table 1"
- Automatically disappears after 3 seconds
- JavaScript controls when it shows and what message it displays

---

### 9. Script Loading

```html
<script src="app.js"></script>
```

**Explanation:**
- Placed at the END of the body (not in head)
- **Why at the end?** - Ensures all HTML elements exist before JavaScript tries to access them
- The `DOMContentLoaded` event in app.js provides additional safety

---

## Element ID Reference Table

| ID | Element | Used For |
|----|---------|----------|
| `currentTime` | span | Displays current date/time |
| `tableSearch` | input | Filter tables by name |
| `tablesGrid` | div | Container for table cards |
| `menuSearch` | input | Search menu items |
| `menuItems` | div | Container for menu items |
| `orderModal` | div | Order details popup |
| `modalTableName` | h3 | Shows table name in modal |
| `closeModal` | button | Close order modal |
| `orderItems` | div | List of ordered items |
| `orderEmpty` | div | "No items" message |
| `subtotal` | span | Subtotal amount |
| `tax` | span | Tax amount |
| `grandTotal` | span | Total amount |
| `clearOrderBtn` | button | Clear all items |
| `generateBillBtn` | button | Generate bill |
| `billModal` | div | Bill popup |
| `closeBillModal` | button | Close bill modal |
| `billContent` | div | Formatted bill |
| `printBillBtn` | button | Print the bill |
| `endSessionBtn` | button | End table session |
| `toast` | div | Notification container |
| `toastMessage` | span | Notification text |

---

## Font Awesome Icons Used

| Icon Class | Meaning |
|------------|---------|
| `fa-utensils` | Fork and knife (logo) |
| `fa-chair` | Tables section |
| `fa-book-open` | Menu section |
| `fa-search` | Search boxes |
| `fa-times` | Close buttons |
| `fa-shopping-cart` | Empty order |
| `fa-trash` | Delete/Clear |
| `fa-receipt` | Bill/Generate bill |
| `fa-print` | Print |
| `fa-check` | Confirm/End session |
| `fa-check-circle` | Success notification |
| `fa-plus` | Add/Increase |
| `fa-minus` | Decrease |
| `fa-shopping-basket` | Items count |
