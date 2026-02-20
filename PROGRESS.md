# Connolly App — Session Handover
**Date:** 20 February 2026  
**Session transcript:** 2026-02-20-15-04-46-orders-dashboard-password-gdpr-protection.txt  

---

## What we've built so far

### orders-dashboard.html — COMPLETED THIS SESSION ✅
Full warehouse orders dashboard. Everything below is in the current file.

**Password protection**
- Login screen shown on every page load
- Default password: `Orders4!`
- Password is changeable from inside the dashboard (🔑 Change Password button)
- New password saved to browser localStorage — persists across sessions
- Lock button (🔒) signs out immediately
- Session stored in sessionStorage — expires when tab closes

**Search**
- Search box filters all orders in real time
- Searches: customer name, email, phone, address, postcode, country, staff member, tracking number
- Matching text is highlighted in gold
- Result count shown while searching
- × button to clear search
- Works on both Active and Archive tabs

**Archiving**
- Active orders tab has a 📦 Archive button per order
- Confirmation dialog before archiving
- Archived orders move to Firebase path `shipping-orders-archive` (separate from `shipping-orders`)
- Archive tab shows all archived orders with grey "Archived" badge
- ↩ Restore button on archived orders moves them back to active
- Both tabs show a count badge

**Print fix**
- Print button prints only the target order (fixed the 13-blank-pages bug)
- Print styles: white background, black text, hides buttons and receipt images
- Print header "Connolly — Order Sheet" appears at top of printed page

**Status workflow**
- Pending → Start Processing → Mark Complete → (Reopen if needed)
- Archive button available on any status

---

## Next task — NOT YET DONE ❌

### Sequential Order IDs on submit-order.html
**Goal:** Orders should be numbered sequentially (000001, 000002...) instead of random Firebase keys  
**How:** When a new order is submitted, read a counter from Firebase, increment it, save the order with the formatted ID as a field, save the counter back  
**Firebase path for counter:** `order-id-counter` (a single integer)  
**Format:** 6-digit zero-padded string, e.g. `000001`, `000042`, `001337`  
**Display:** Show as "Order #000001" in the dashboard order header instead of the truncated Firebase key  

**To do this, Claude needs:**
- The raw GitHub URL for `submit-order.html`
- Ask Jem to paste it at the start of the next session

**Dashboard change also needed:** Once order IDs are sequential, update the order header in `orders-dashboard.html` to show `Order #${order.orderNumber}` instead of `Order ID: ${orderId.slice(0,8)}`

---

## Firebase structure (for reference)

```
swis-9b0ce (Firebase project)
├── shipping-orders/          ← active orders (Firebase push keys)
│   └── {pushKey}/
│       ├── timestamp
│       ├── status            (pending / processing / completed)
│       ├── staffMember
│       ├── dutyPaid
│       ├── trackingNumber
│       ├── receiptUrl
│       ├── additionalNotes
│       ├── customer/
│       │   ├── name, email, phone, vatEori
│       │   └── address/ (line1, line2, line3, line4, postcode)
│       ├── calculation/
│       │   ├── country, totalGoods, totalDuty, totalTax
│       │   ├── handlingFee, grandTotal, shippingCost, boxSize
│       └── itemDetails[]     (description, composition, origin)
│
├── shipping-orders-archive/  ← archived orders (same structure + archivedAt field)
│
└── order-id-counter          ← TO BE CREATED: integer, starts at 0
```

---

## GitHub repo
https://github.com/jkconnollyengland-stack/connolly-calculator

Main files:
- `connolly-calculator-with-admin.html` — main duty/tax calculator
- `orders-dashboard.html` — warehouse orders dashboard (updated this session)
- `submit-order.html` — order submission form (next session)

---

## User notes
- Jem, 61, non-technical — needs simple step-by-step instructions for GitHub edits
- GitHub method: edit file → find-and-replace → commit changes
- For new files: create file → paste content → commit
- Hard refresh after upload: Ctrl+Shift+R
- Admin password for calculator: `connolly2025`
- Orders dashboard password: `Orders4!` (changeable in-app)
