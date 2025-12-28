# Restaurant Billing System - JavaScript Documentation

## Overview
The `app.js` file contains all the logic for the Restaurant Billing System. It handles:
- Menu and table rendering
- Drag and drop functionality (desktop & mobile)
- Order management
- Bill generation
- Data persistence with localStorage

---

## Recent Updates (Dec 2025)

### Table search: safer + more consistent
Table filtering was updated to behave more reliably (similar to menu search).

**What changed:**
- `applyTableFilters()` now trims the search input before filtering:
    - Prevents accidental spaces from breaking matches.
- `setupEventListeners()` now checks `tableSearchInput` exists before adding the `input` listener:
    - Prevents runtime errors if the table search input is missing/renamed.

**Related functions:**
- `applyTableFilters()`
- `setupEventListeners()`

---

## Data Structures

### 1. menuData Array
```javascript
const menuData = [
    { id: 1, name: "Chicken 65", category: "starters", price: 220, icon: "fas fa-drumstick-bite" },
    { id: 2, name: "Paneer Tikka", category: "starters", price: 200, icon: "fas fa-cheese" },
    // ... 32 items total
];
```

**Explanation:**
- Contains all 32 menu items
- Each item has:
  - `id` - Unique identifier for the item
  - `name` - Display name
  - `category` - Used for filtering (starters, soups, main-course, desserts, beverages)
  - `price` - Price in Rupees
  - `icon` - Font Awesome icon class for visual display

**Why use an array of objects?**
- Easy to loop through and render
- Can filter/sort without modifying original data
- Can add new items without changing code structure

---

### 2. tablesData Array
```javascript
const tablesData = [
    { id: 1, name: "Table 1", capacity: 4 },
    { id: 2, name: "Table 2", capacity: 4 },
    // ... 12 tables total
];
```

**Explanation:**
- Defines all 12 restaurant tables
- `capacity` is stored but not currently displayed (future feature)

---

### 3. orders Object
```javascript
let orders = {};
// Structure after adding items:
// {
//     1: [{ id: 11, name: "Hyderabadi Biryani", price: 350, quantity: 2, category: "main-course" }],
//     2: [{ id: 27, name: "Fresh Lime Soda", price: 60, quantity: 1, category: "beverages" }]
// }
```

**Explanation:**
- Keys are table IDs (1, 2, 3, etc.)
- Values are arrays of ordered items
- Each ordered item has quantity tracking
- Empty tables don't have a key in this object

---

### 4. Global Variables
```javascript
let currentTableId = null;  // Which table's order modal is open
let draggedItem = null;     // Currently dragged menu item (for desktop drag)
```

---

## DOM Element References

```javascript
const menuItemsContainer = document.getElementById('menuItems');
const tablesGridContainer = document.getElementById('tablesGrid');
const menuSearchInput = document.getElementById('menuSearch');
// ... many more
```

**Why store these in variables?**
- Performance: `getElementById` is called once, not every time we need the element
- Cleaner code: Shorter variable names instead of long `document.getElementById()` calls
- The elements exist because script is at end of body

---

## Initialization

### initializeApp()
```javascript
function initializeApp() {
    loadOrders();           // Load saved orders from localStorage
    renderMenuItems();      // Display all menu items
    renderTables();         // Display all tables
    setupEventListeners();  // Attach click, input, drag handlers
    updateTime();           // Show current time
    setInterval(updateTime, 1000);  // Update time every second
}
```

**Explanation:**
- Called when page loads (via DOMContentLoaded event)
- Sets up the entire application
- `setInterval(updateTime, 1000)` - Calls updateTime every 1000ms (1 second)

---

## Data Persistence

### loadOrders()
```javascript
function loadOrders() {
    const savedOrders = localStorage.getItem('restaurantOrders');
    if (savedOrders) {
        orders = JSON.parse(savedOrders);
    }
}
```

**Explanation:**
- Retrieves saved orders from browser's localStorage
- `JSON.parse()` converts the stored string back to JavaScript object
- If no saved data exists, `orders` stays as empty object `{}`

**Why use localStorage?**
- Data persists even after browser is closed
- No server/database needed
- Each browser has its own storage

### saveOrders()
```javascript
function saveOrders() {
    localStorage.setItem('restaurantOrders', JSON.stringify(orders));
}
```

**Explanation:**
- Saves current orders to localStorage
- `JSON.stringify()` converts the object to a string (localStorage only stores strings)
- Called every time an order is modified

---

## Time Display

### updateTime()
```javascript
function updateTime() {
    const timeEl = document.getElementById('currentTime');
    if (!timeEl) return;  // Safety check
    const now = new Date();
    const options = { 
        weekday: 'short',   // "Sat"
        year: 'numeric',    // "2025"
        month: 'short',     // "Dec"
        day: 'numeric',     // "28"
        hour: '2-digit',    // "22"
        minute: '2-digit'   // "45"
    };
    timeEl.textContent = now.toLocaleDateString('en-IN', options);
}
```

**Explanation:**
- Creates a new Date object with current date/time
- Formats it for Indian locale (en-IN)
- Updates the header time display
- Called every second by setInterval

**Output example:** "Sat, 28 Dec 2025, 10:45 PM"

---

## Rendering Functions

### renderMenuItems(filter = {})
```javascript
function renderMenuItems(filter = {}) {
    let items = [...menuData];  // Create copy of menu data
    
    // Filter by search term
    if (filter.search) {
        const searchTerm = filter.search.toLowerCase();
        items = items.filter(item => 
            item.name.toLowerCase().includes(searchTerm) ||
            item.category.toLowerCase().includes(searchTerm)
        );
    }
    
    // Filter by category
    if (filter.category && filter.category !== 'all') {
        items = items.filter(item => item.category === filter.category);
    }
    
    // Sort items
    if (filter.sort) {
        switch (filter.sort) {
            case 'name-asc':
                items.sort((a, b) => a.name.localeCompare(b.name));
                break;
            case 'price-desc':
                items.sort((a, b) => b.price - a.price);
                break;
            // ... other cases
        }
    }
    
    // Generate HTML
    menuItemsContainer.innerHTML = items.map(item => `
        <div class="menu-item" draggable="true" data-item-id="${item.id}">
            ...
        </div>
    `).join('');
    
    // Attach event listeners
    document.querySelectorAll('.menu-item').forEach(item => {
        item.addEventListener('dragstart', handleDragStart);
        item.addEventListener('dragend', handleDragEnd);
        item.addEventListener('touchstart', handleTouchStart, { passive: false });
        item.addEventListener('touchmove', handleTouchMove, { passive: false });
        item.addEventListener('touchend', handleTouchEnd);
    });
}
```

**Explanation:**
1. `[...menuData]` - Creates a copy so we don't modify original
2. Applies filters (search, category) if provided
3. Applies sorting if requested
4. Uses `.map()` to convert each item object to HTML string
5. `.join('')` combines all HTML strings into one
6. Attaches drag/touch handlers to each item

**Why `draggable="true"`?**
- Enables HTML5 drag and drop API
- Allows items to be dragged to tables

**Why `data-item-id`?**
- Custom data attribute to store the item's ID
- Used to identify which item was dropped on a table

---

### renderTables(searchTerm = '')
```javascript
function renderTables(searchTerm = '') {
    let tables = [...tablesData];
    
    if (searchTerm) {
        tables = tables.filter(table => 
            table.name.toLowerCase().includes(searchTerm.toLowerCase())
        );
    }
    
    tablesGridContainer.innerHTML = tables.map(table => {
        const tableOrder = orders[table.id] || [];
        const itemCount = tableOrder.reduce((sum, item) => sum + item.quantity, 0);
        const total = tableOrder.reduce((sum, item) => sum + (item.price * item.quantity), 0);
        const hasOrders = tableOrder.length > 0;
        
        return `
            <div class="table-card ${hasOrders ? 'has-orders' : ''}" 
                 data-table-id="${table.id}"
                 ondragover="handleDragOver(event)"
                 ondragleave="handleDragLeave(event)"
                 ondrop="handleDrop(event)">
                ...
            </div>
        `;
    }).join('');
    
    // Add click handlers
    document.querySelectorAll('.table-card').forEach(card => {
        card.addEventListener('click', (e) => {
            openOrderModal(parseInt(card.dataset.tableId));
        });
    });
}
```

**Explanation:**
1. Gets orders for each table from `orders` object
2. Calculates total items and total price using `.reduce()`
3. Adds `has-orders` class if table has items (for styling)
4. Adds drag event handlers as inline attributes (ondragover, ondrop)
5. Adds click handler to open the order modal

**Why `orders[table.id] || []`?**
- If table has no orders, `orders[table.id]` is `undefined`
- `|| []` provides empty array as default to prevent errors

---

## Drag and Drop (Desktop)

### handleDragStart(e)
```javascript
function handleDragStart(e) {
    if ('ontouchstart' in window) {
        e.preventDefault();
        return;  // Don't use this on touch devices
    }
    
    draggedItem = e.target;
    e.target.classList.add('dragging');
    e.dataTransfer.effectAllowed = 'copy';
    e.dataTransfer.setData('text/plain', e.target.dataset.itemId);
    document.addEventListener('dragover', handleDragAutoScroll);
}
```

**Explanation:**
- `e.target` - The menu item being dragged
- `effectAllowed = 'copy'` - Shows copy cursor during drag
- `setData()` - Stores the item ID to retrieve when dropped
- Adds auto-scroll listener for edge scrolling

### handleDragOver(e)
```javascript
function handleDragOver(e) {
    e.preventDefault();  // REQUIRED to allow dropping
    e.dataTransfer.dropEffect = 'copy';
    const tableCard = e.target.closest('.table-card');
    if (tableCard) {
        tableCard.classList.add('drag-over');  // Visual feedback
    }
}
```

**Why `e.preventDefault()`?**
- By default, dropping is not allowed on elements
- `preventDefault()` enables the drop event to fire

### handleDrop(e)
```javascript
function handleDrop(e) {
    e.preventDefault();
    const tableCard = e.target.closest('.table-card');
    if (tableCard) {
        tableCard.classList.remove('drag-over');
        const itemId = parseInt(e.dataTransfer.getData('text/plain'));
        const tableId = parseInt(tableCard.dataset.tableId);
        addItemToOrder(tableId, itemId);
    }
}
```

**Explanation:**
1. Gets the item ID from the drag data
2. Gets the table ID from the card's data attribute
3. Calls `addItemToOrder()` to add the item

---

## Touch Drag (Mobile)

Mobile devices don't support HTML5 drag and drop, so we implement custom touch handling.

### handleTouchStart(e)
```javascript
function handleTouchStart(e) {
    touchedMenuItem = e.target.closest('.menu-item');
    touchStartX = e.touches[0].clientX;
    touchStartY = e.touches[0].clientY;
    
    // Start drag after 400ms hold
    touchTimeout = setTimeout(() => {
        if (touchedMenuItem && !hasMoved) {
            isDragging = true;
            createTouchGhost(touchedMenuItem, touchStartX, touchStartY);
            if (navigator.vibrate) {
                navigator.vibrate(50);  // Haptic feedback
            }
        }
    }, 400);
}
```

**Explanation:**
- Waits 400ms before starting drag (to distinguish from scroll)
- Creates a "ghost" copy of the item that follows the finger
- Vibrates phone as feedback (if supported)

### createTouchGhost(element, x, y)
```javascript
function createTouchGhost(element, x, y) {
    touchGhost = element.cloneNode(true);  // Clone the element
    touchGhost.classList.add('drag-ghost');
    touchGhost.style.cssText = `
        position: fixed !important;
        pointer-events: none !important;
        z-index: 99999 !important;
        opacity: 0.95;
        transform: rotate(3deg) scale(1.02);
        box-shadow: 0 15px 40px rgba(0,0,0,0.3);
    `;
    document.body.appendChild(touchGhost);
}
```

**Explanation:**
- Creates a visual copy that follows the finger
- `pointer-events: none` - Allows detecting elements under the ghost
- Slight rotation and scale for visual effect

### handleTouchMove(e)
```javascript
function handleTouchMove(e) {
    if (isDragging && touchGhost) {
        e.preventDefault();  // Prevent page scrolling
        
        // Move ghost to finger position
        touchGhost.style.left = (touch.clientX - (ghostWidth / 2)) + 'px';
        touchGhost.style.top = (touch.clientY - 40) + 'px';
        
        // Auto-scroll when near edges
        // ... (auto-scroll logic)
        
        // Find element under finger
        touchGhost.style.display = 'none';  // Hide ghost temporarily
        const elementBelow = document.elementFromPoint(touch.clientX, touch.clientY);
        touchGhost.style.display = 'block';
        
        // Highlight table if over one
        const tableCard = elementBelow?.closest('.table-card');
        if (tableCard) {
            tableCard.classList.add('drag-over');
        }
    }
}
```

**Explanation:**
- Moves ghost element to follow finger
- Implements auto-scrolling when finger is near screen edges
- Detects which table is under the finger

### handleTouchEnd(e)
```javascript
function handleTouchEnd(e) {
    if (touchedMenuItem && touchGhost && isDragging) {
        // Find table under finger
        const elementBelow = document.elementFromPoint(touch.clientX, touch.clientY);
        const tableCard = elementBelow?.closest('.table-card');
        
        if (tableCard) {
            const itemId = parseInt(touchedMenuItem.dataset.itemId);
            const tableId = parseInt(tableCard.dataset.tableId);
            addItemToOrder(tableId, itemId);
        }
    }
    cleanupTouchDrag();  // Remove ghost, reset state
}
```

---

## Order Management

### addItemToOrder(tableId, itemId)
```javascript
function addItemToOrder(tableId, itemId) {
    const menuItem = menuData.find(item => item.id === itemId);
    if (!menuItem) return;
    
    if (!orders[tableId]) {
        orders[tableId] = [];  // Create empty array for new table
    }
    
    const existingItem = orders[tableId].find(item => item.id === itemId);
    if (existingItem) {
        existingItem.quantity += 1;  // Increase quantity if exists
    } else {
        orders[tableId].push({
            id: menuItem.id,
            name: menuItem.name,
            price: menuItem.price,
            quantity: 1,
            category: menuItem.category
        });
    }
    
    saveOrders();
    renderTables(tableSearchInput.value);
    showToast(`${menuItem.name} added to ${tablesData.find(t => t.id === tableId).name}`);
}
```

**Explanation:**
1. Finds the menu item by ID
2. Creates order array for table if doesn't exist
3. If item already in order, increases quantity
4. Otherwise, adds new item with quantity 1
5. Saves to localStorage
6. Re-renders tables to update counts
7. Shows success toast message

---

### updateQuantity(itemId, change)
```javascript
function updateQuantity(itemId, change) {
    const tableOrder = orders[currentTableId];
    const item = tableOrder.find(i => i.id === itemId);
    if (item) {
        item.quantity += change;  // +1 or -1
        if (item.quantity <= 0) {
            removeItem(itemId);
            return;
        }
        saveOrders();
        renderOrderItems();
        renderTables(tableSearchInput.value);
    }
}
```

**Explanation:**
- `change` is +1 (increase) or -1 (decrease)
- If quantity reaches 0, removes item completely
- Updates localStorage and re-renders

---

### removeItem(itemId)
```javascript
function removeItem(itemId) {
    const tableOrder = orders[currentTableId];
    const itemIndex = tableOrder.findIndex(i => i.id === itemId);
    
    if (itemIndex !== -1) {
        tableOrder.splice(itemIndex, 1);  // Remove item from array
        
        if (tableOrder.length === 0) {
            delete orders[currentTableId];  // Remove empty table
        }
        
        saveOrders();
        renderOrderItems();
        renderTables(tableSearchInput.value);
    }
}
```

---

### updateOrderSummary()
```javascript
function updateOrderSummary() {
    const tableOrder = orders[currentTableId] || [];
    const subtotal = tableOrder.reduce((sum, item) => sum + (item.price * item.quantity), 0);
    const tax = subtotal * 0.05;  // 5% tax
    const total = subtotal + tax;
    
    subtotalEl.textContent = `₹${subtotal.toFixed(2)}`;
    taxEl.textContent = `₹${tax.toFixed(2)}`;
    grandTotalEl.textContent = `₹${total.toFixed(2)}`;
}
```

**Explanation:**
- Uses `.reduce()` to calculate sum of all items
- Calculates 5% tax
- Updates the modal footer with formatted amounts

---

## Bill Generation

### generateBill()
```javascript
function generateBill() {
    const tableOrder = orders[currentTableId];
    if (!tableOrder || tableOrder.length === 0) {
        showToast('No items to generate bill', 'warning');
        return;
    }
    
    const table = tablesData.find(t => t.id === currentTableId);
    const subtotal = tableOrder.reduce((sum, item) => sum + (item.price * item.quantity), 0);
    const tax = subtotal * 0.05;
    const total = subtotal + tax;
    const now = new Date();
    
    billContent.innerHTML = `
        <div class="bill-header">
            <h4>Hyderabad's Finest</h4>
            <p>Restaurant & Fine Dining</p>
            <p>${now.toLocaleDateString('en-IN')} ${now.toLocaleTimeString('en-IN')}</p>
        </div>
        <div class="bill-table-info">${table.name}</div>
        <div class="bill-items">
            ${tableOrder.map(item => `
                <div class="bill-item">
                    <span class="bill-item-name">${item.name}</span>
                    <span class="bill-item-qty">${item.quantity}</span>
                    <span class="bill-item-price">₹${(item.price * item.quantity).toFixed(2)}</span>
                </div>
            `).join('')}
        </div>
        <div class="bill-totals">
            <div class="bill-total-row grand-total">
                <span>Grand Total:</span>
                <span>₹${total.toFixed(2)}</span>
            </div>
        </div>
        <div class="bill-footer">
            <p>Thank you for dining with us!</p>
        </div>
    `;
    
    orderModal.classList.remove('active');
    billModal.classList.add('active');
}
```

**Explanation:**
- Creates formatted bill HTML
- Shows restaurant name, date/time, table name
- Lists all items with quantities and prices
- Shows totals
- Closes order modal, opens bill modal

---

## Event Listeners

### setupEventListeners()
```javascript
function setupEventListeners() {
    // Search input - filter menu as user types
    menuSearchInput.addEventListener('input', () => {
        applyFilters();
    });
    
    // Table search
    tableSearchInput.addEventListener('input', () => {
        renderTables(tableSearchInput.value);
    });
    
    // Close modal buttons
    closeModalBtn.addEventListener('click', () => {
        orderModal.classList.remove('active');
    });
    
    // Click outside modal to close
    orderModal.addEventListener('click', (e) => {
        if (e.target === orderModal) {  // Clicked on overlay, not modal
            orderModal.classList.remove('active');
        }
    });
    
    // Escape key to close modals
    document.addEventListener('keydown', (e) => {
        if (e.key === 'Escape') {
            orderModal.classList.remove('active');
            billModal.classList.remove('active');
        }
    });
    
    // Button handlers
    clearOrderBtn.addEventListener('click', clearOrder);
    generateBillBtn.addEventListener('click', generateBill);
    printBillBtn.addEventListener('click', printBill);
    endSessionBtn.addEventListener('click', endSession);
}
```

---

## Utility Functions

### showToast(message, type = 'success')
```javascript
function showToast(message, type = 'success') {
    toastMessage.textContent = message;
    toast.classList.add('show');
    setTimeout(() => {
        toast.classList.remove('show');
    }, 3000);  // Hide after 3 seconds
}
```

### formatCategory(category)
```javascript
function formatCategory(category) {
    return category.replace('-', ' ').replace(/\b\w/g, l => l.toUpperCase());
}
// "main-course" -> "Main Course"
```

---

## Global Window Functions

```javascript
window.handleDragOver = handleDragOver;
window.handleDragLeave = handleDragLeave;
window.handleDrop = handleDrop;
window.updateQuantity = updateQuantity;
window.removeItem = removeItem;
window.startNewSession = startNewSession;
```

**Why expose to window?**
- These functions are called from HTML (onclick, ondragover, etc.)
- HTML can only call functions on the global `window` object
- `window.functionName = functionName` makes them globally accessible
