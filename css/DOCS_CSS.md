# Restaurant Billing System - CSS Documentation

## Overview
The `styles.css` file contains all the styling for the Restaurant Billing System. It uses:
- CSS Variables for theming
- CSS Grid and Flexbox for layouts
- Media queries for responsive design
- Print styles for bill printing

---

## CSS Variables (Custom Properties)

```css
:root {
    --primary-color: #e74c3c;      /* Red - main brand color */
    --primary-dark: #c0392b;        /* Darker red for hover states */
    --secondary-color: #2c3e50;     /* Dark blue-gray */
    --accent-color: #f39c12;        /* Orange/Gold - for highlights */
    --success-color: #27ae60;       /* Green - for success states, prices */
    --warning-color: #f1c40f;       /* Yellow - for warnings */
    --danger-color: #e74c3c;        /* Red - for delete buttons */
    --light-bg: #f8f9fa;            /* Light gray background */
    --card-bg: #ffffff;             /* White card background */
    --text-primary: #2c3e50;        /* Main text color */
    --text-secondary: #7f8c8d;      /* Secondary/muted text */
    --border-color: #e0e0e0;        /* Border color */
    --shadow: 0 2px 10px rgba(0, 0, 0, 0.1);      /* Small shadow */
    --shadow-lg: 0 5px 20px rgba(0, 0, 0, 0.15);  /* Large shadow */
    --radius: 12px;                 /* Large border radius */
    --radius-sm: 8px;               /* Small border radius */
    --transition: all 0.3s ease;    /* Standard transition */
}
```

**Why use CSS Variables?**
- Change colors in ONE place to update entire app
- Easy theming (could add dark mode by changing variables)
- Consistent values across all styles
- Use: `var(--primary-color)` anywhere

---

## Reset & Base Styles

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
```

**Explanation:**
- `*` selects ALL elements
- Removes default browser margins/padding
- `box-sizing: border-box` - padding is included in width (much easier to work with)

```css
body {
    font-family: 'Poppins', sans-serif;
    background-color: var(--light-bg);
    color: var(--text-primary);
    line-height: 1.6;
    min-height: 100vh;
}
```

**Explanation:**
- Uses Poppins font from Google Fonts
- Light gray background for the whole page
- `min-height: 100vh` - minimum full viewport height

---

## Layout Structure

### App Container
```css
.app-container {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
}
```
- Fills full screen height
- Flexbox column layout (header on top, content below)

### Main Content Grid
```css
.main-content {
    display: grid;
    grid-template-columns: 1fr 2fr;
    gap: 20px;
    padding: 20px;
    flex: 1;
}
```

**Explanation:**
- `grid-template-columns: 1fr 2fr` - Two columns:
  - Tables section: 1 fraction (33% of space)
  - Menu section: 2 fractions (67% of space)
- `gap: 20px` - Space between columns
- `flex: 1` - Takes remaining vertical space after header

---

## Header Styles

```css
.header {
    background: linear-gradient(135deg, var(--primary-color), var(--primary-dark));
    color: white;
    padding: 15px 25px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    box-shadow: var(--shadow-lg);
    position: sticky;
    top: 0;
    z-index: 100;
}
```

**Explanation:**
- `linear-gradient` - Red gradient from top-left to bottom-right
- `position: sticky; top: 0` - Header stays at top when scrolling
- `z-index: 100` - Header stays above other content
- Flexbox with `space-between` puts logo on left, time on right

---

## Button Styles

```css
.btn {
    padding: 10px 20px;
    border: none;
    border-radius: var(--radius-sm);
    cursor: pointer;
    font-family: inherit;
    font-size: 0.95rem;
    font-weight: 500;
    display: inline-flex;
    align-items: center;
    gap: 8px;
    transition: var(--transition);
}
```

**Explanation:**
- Base styles for all buttons
- `inline-flex` - Allows icon and text side by side
- `gap: 8px` - Space between icon and text
- `font-family: inherit` - Uses same font as parent (Poppins)

### Button Variants
```css
.btn-primary { background-color: white; color: var(--primary-color); }
.btn-secondary { background-color: var(--text-secondary); color: white; }
.btn-success { background-color: var(--success-color); color: white; }
.btn-danger { background-color: var(--danger-color); color: white; }
```

Each button type has its own color scheme.

---

## Section Styles (Menu & Tables)

```css
.menu-section,
.tables-section {
    background-color: var(--card-bg);
    border-radius: var(--radius);
    padding: 20px;
    box-shadow: var(--shadow);
    display: flex;
    flex-direction: column;
    max-height: calc(100vh - 120px);
    overflow: hidden;
}
```

**Explanation:**
- White card with rounded corners and shadow
- `max-height: calc(100vh - 120px)` - Maximum height is viewport minus header
- `overflow: hidden` - Content that overflows is hidden (scrollable containers inside)
- Flexbox column for internal layout

---

## Search Box Styles

```css
.search-box {
    flex: 1;
    min-width: 200px;
    position: relative;
}

.search-box i {
    position: absolute;
    left: 15px;
    top: 50%;
    transform: translateY(-50%);
    color: var(--text-secondary);
}

.search-box input {
    width: 100%;
    padding: 12px 15px 12px 45px;
    border: 2px solid var(--border-color);
    border-radius: var(--radius-sm);
}

.search-box input:focus {
    outline: none;
    border-color: var(--primary-color);
}
```

**Explanation:**
- Icon is positioned absolutely inside the input
- `transform: translateY(-50%)` - Centers icon vertically
- `padding-left: 45px` - Makes room for the icon
- `:focus` state changes border color (visual feedback)

---

## Menu Items Grid

```css
.menu-items {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
    gap: 15px;
    overflow-y: auto;
    padding: 5px 10px 5px 5px;
    flex: 1;
}
```

**Explanation:**
- `repeat(auto-fill, minmax(180px, 1fr))`:
  - Creates as many columns as fit
  - Each column is minimum 180px, maximum 1 fraction
  - Automatically adjusts based on container width
- `overflow-y: auto` - Vertical scrollbar when needed
- `flex: 1` - Takes remaining space in section

### Custom Scrollbar
```css
.menu-items::-webkit-scrollbar { width: 6px; }
.menu-items::-webkit-scrollbar-track { background: var(--light-bg); }
.menu-items::-webkit-scrollbar-thumb { background: var(--border-color); }
```

**Explanation:**
- Custom thin scrollbar for webkit browsers (Chrome, Safari, Edge)
- Makes scrollbar less intrusive

---

## Menu Item Card

```css
.menu-item {
    background: linear-gradient(145deg, #ffffff, #f5f5f5);
    border-radius: var(--radius-sm);
    padding: 15px 15px 20px 15px;
    cursor: grab;
    transition: var(--transition);
    border: 2px solid transparent;
    -webkit-touch-callout: none !important;
    -webkit-user-select: none !important;
    user-select: none !important;
}
```

**Explanation:**
- Subtle gradient background (white to light gray)
- `cursor: grab` - Indicates item is draggable
- `border: 2px solid transparent` - Reserve space for hover border
- `-webkit-user-select: none` - Prevents text selection (for touch drag)

### Hover & Drag States
```css
.menu-item:hover {
    transform: scale(1.02);
    box-shadow: var(--shadow-lg);
    border-color: var(--primary-color);
    z-index: 10;
}

.menu-item.dragging {
    opacity: 0.5;
    transform: scale(1.05);
}
```

**Explanation:**
- Hover: Slight scale up, shadow, red border
- `z-index: 10` - Hovered item appears above neighbors
- Dragging: Faded appearance

---

## Table Card Styles

```css
.table-card {
    background: linear-gradient(145deg, #ffffff, #f5f5f5);
    border-radius: var(--radius);
    padding: 20px;
    transition: var(--transition);
    border: 3px dashed transparent;
    cursor: pointer;
}
```

### Drag-Over State
```css
.table-card.drag-over {
    border-color: var(--success-color);
    background-color: rgba(39, 174, 96, 0.15);
    box-shadow: 0 0 0 4px rgba(39, 174, 96, 0.2), var(--shadow-lg);
}
```

**Explanation:**
- When dragging a menu item over a table:
  - Green dashed border
  - Green tinted background
  - Glowing green shadow
- Visual feedback that drop is valid

### Has-Orders State
```css
.table-card.has-orders {
    border: 2px solid var(--success-color);
}
```
- Solid green border when table has items

---

## Table Status Badges

```css
.table-status {
    padding: 4px 10px;
    border-radius: 20px;
    font-size: 0.7rem;
    font-weight: 600;
    text-transform: uppercase;
}

.table-status.available {
    background-color: #e8f5e9;  /* Light green */
    color: var(--success-color);
}

.table-status.occupied {
    background-color: #fff3e0;  /* Light orange */
    color: var(--accent-color);
}
```

**Explanation:**
- Pill-shaped badges (high border-radius)
- Available: Green text on light green background
- Occupied: Orange text on light orange background

---

## Modal Styles

### Overlay
```css
.modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(0, 0, 0, 0.6);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1000;
    opacity: 0;
    visibility: hidden;
    transition: var(--transition);
}

.modal-overlay.active {
    opacity: 1;
    visibility: visible;
}
```

**Explanation:**
- Covers entire screen (`position: fixed`, all sides 0)
- Semi-transparent black background
- Initially hidden (`opacity: 0`, `visibility: hidden`)
- `.active` class shows it
- Flexbox centers the modal

### Modal Box
```css
.modal {
    background-color: white;
    border-radius: var(--radius);
    width: 100%;
    max-width: 550px;
    max-height: 90vh;
    overflow: hidden;
    display: flex;
    flex-direction: column;
    transform: scale(0.9);
    transition: var(--transition);
}

.modal-overlay.active .modal {
    transform: scale(1);
}
```

**Explanation:**
- White box with rounded corners
- `max-width: 550px` - Doesn't get too wide
- `max-height: 90vh` - Maximum 90% of viewport height
- `transform: scale(0.9)` to `scale(1)` - Zoom-in animation when opening

---

## Order Item Row

```css
.order-item {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 15px;
    background-color: var(--light-bg);
    border-radius: var(--radius-sm);
    gap: 15px;
}
```

**Explanation:**
- Flexbox row with space between items
- Gray background to distinguish from modal background
- Contains: item info, quantity controls, total price

### Quantity Controls
```css
.quantity-control {
    display: flex;
    align-items: center;
    gap: 8px;
    background-color: white;
    border-radius: var(--radius-sm);
    padding: 5px;
}

.quantity-btn {
    width: 32px;
    height: 32px;
    border: none;
    border-radius: 50%;
    background-color: var(--primary-color);
    color: white;
    cursor: pointer;
}
```

**Explanation:**
- Row with - button, number, + button
- Circular buttons (border-radius: 50%)
- White background container for contrast

---

## Toast Notification

```css
.toast {
    position: fixed;
    bottom: 30px;
    right: 30px;
    transform: translateY(100px);
    background-color: var(--success-color);
    color: white;
    padding: 15px 25px;
    border-radius: var(--radius);
    display: flex;
    align-items: center;
    gap: 10px;
    box-shadow: var(--shadow-lg);
    opacity: 0;
    visibility: hidden;
    transition: var(--transition);
    z-index: 2000;
}

.toast.show {
    opacity: 1;
    visibility: visible;
    transform: translateY(0);
}
```

**Explanation:**
- Fixed position bottom-right corner
- Initially hidden and moved down (`translateY(100px)`)
- `.show` class slides it up and fades in
- High z-index so it appears above modals

---

## Drag Ghost (Touch)

```css
.drag-ghost {
    position: fixed !important;
    pointer-events: none !important;
    z-index: 9999 !important;
    opacity: 0.95;
    transform: rotate(3deg) scale(1.05);
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
    border: 2px solid var(--primary-color);
}
```

**Explanation:**
- The ghost element that follows your finger on mobile
- `pointer-events: none` - Allows detecting elements underneath
- `z-index: 9999` - Always on top
- Slight rotation and scale for visual effect
- Red border and shadow for visibility

---

## Responsive Design (Media Queries)

### Tablet (1024px and below)
```css
@media (max-width: 1024px) {
    .main-content {
        grid-template-columns: 1fr;  /* Single column */
    }
    .menu-section,
    .tables-section {
        max-height: none;  /* Remove height limit */
    }
}
```

### Mobile (768px and below)
```css
@media (max-width: 768px) {
    .header {
        flex-direction: column;  /* Stack logo and time */
        gap: 12px;
    }
    
    /* Reorder sections for mobile */
    .menu-section { order: 1; }   /* Menu first */
    .tables-section { order: 2; } /* Tables second */
    
    .tables-grid {
        grid-template-columns: repeat(3, 1fr);  /* 3 columns for tables */
    }
    
    .table-header {
        flex-direction: column;  /* Stack name and status */
    }
}
```

**Why use order property?**
- In HTML, tables section comes before menu section (for desktop left/right layout)
- On mobile, we want menu on top, tables below
- `order` property changes the visual order without changing HTML

### Small Mobile (480px and below)
```css
@media (max-width: 480px) {
    .tables-grid {
        grid-template-columns: repeat(2, 1fr);  /* 2 columns */
    }
    .modal-actions {
        flex-direction: column;  /* Stack buttons */
    }
}
```

---

## Print Styles

```css
@media print {
    * {
        visibility: hidden !important;
    }
    
    .app-container,
    .header,
    .main-content,
    #orderModal {
        display: none !important;
    }
    
    #billModal,
    #billModal .bill-modal,
    #billModal .bill-content,
    #billModal .bill-content * {
        visibility: visible !important;
        display: block !important;
    }
    
    #billModal .modal-header,
    #billModal .modal-footer {
        display: none !important;
    }
}
```

**Explanation:**
- `@media print` - Styles only apply when printing
- Hides everything except the bill content
- Removes modal header/footer (Print and End Session buttons)
- Result: Only the receipt prints, not the whole page

---

## Animation

```css
@keyframes pulse {
    0% {
        box-shadow: 0 0 0 0 rgba(39, 174, 96, 0.4);
    }
    70% {
        box-shadow: 0 0 0 15px rgba(39, 174, 96, 0);
    }
    100% {
        box-shadow: 0 0 0 0 rgba(39, 174, 96, 0);
    }
}

.table-card.drag-over {
    animation: pulse 1s infinite;
}
```

**Explanation:**
- Creates pulsing green glow effect
- Applied when dragging over a table
- `infinite` - Repeats until drag ends
- Visual feedback that table will accept the drop

---

## CSS Units Reference

| Unit | Meaning | Example |
|------|---------|---------|
| `px` | Fixed pixels | `padding: 20px` |
| `rem` | Relative to root font size | `font-size: 1.2rem` |
| `%` | Percentage of parent | `width: 100%` |
| `vh` | Viewport height percentage | `max-height: 90vh` |
| `vw` | Viewport width percentage | (not used) |
| `fr` | Grid fraction | `grid-template-columns: 1fr 2fr` |

---

## Selector Types Used

| Selector | Example | Meaning |
|----------|---------|---------|
| Element | `body` | All body elements |
| Class | `.btn` | Elements with class="btn" |
| ID | `#billModal` | Element with id="billModal" |
| Descendant | `.modal-header h3` | h3 inside .modal-header |
| Child | `.order-item > div` | Direct child only |
| Pseudo-class | `:hover`, `:focus` | State-based styles |
| Pseudo-element | `::-webkit-scrollbar` | Style part of element |
| Attribute | `[draggable="true"]` | Elements with attribute |
| Universal | `*` | All elements |

---

## Color Reference

| Variable | Color | Used For |
|----------|-------|----------|
| `--primary-color` | #e74c3c (Red) | Header, buttons, borders |
| `--success-color` | #27ae60 (Green) | Prices, available status, success |
| `--accent-color` | #f39c12 (Orange) | Occupied status, warnings |
| `--text-primary` | #2c3e50 (Dark Blue) | Main text |
| `--text-secondary` | #7f8c8d (Gray) | Secondary text |
