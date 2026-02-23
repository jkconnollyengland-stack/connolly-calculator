# Connolly App — Session Handover
**Date:** 23 February 2026  
**Previous transcript:** 2026-02-23-08-59-27-orders-dashboard-password-gdpr-archive-search.txt

---

## Files and their current state

### orders-dashboard.html ✅ COMPLETE (this session)
All features done and working:
- Password protection (default: `Orders4!`, changeable in-app, stored in localStorage)
- Lock button clears session
- **Search** — real-time filter across name, email, phone, address, country, staff, tracking. Highlights matches in gold.
- **Archive tab** — Archive button per order, confirmation dialog, moves to `shipping-orders-archive` in Firebase. Restore button brings it back.
- **Delete button** — red 🗑️ button on every order (active and archived), confirmation dialog, permanent delete from Firebase
- **Print** — prints single order cleanly, white background, hides buttons
- Status workflow: Pending → Processing → Completed → Reopen
- Order number display: shows `Order #000001` (falls back to truncated Firebase key for old orders)

### submit-order.html ✅ COMPLETE (this session)
- Sequential order IDs: reads/increments `order-id-counter` in Firebase using atomic transaction
- Order saved as `shipping-orders/000001` etc. (number is also stored as `orderNumber` field)
- Success screen shows `Order #000001` in a gold box
- Everything else unchanged from previous version

### connolly-calculator-with-admin.html 🔄 IN PROGRESS
**Change needed:** Restructure the totals display section.

**Current layout** (after all items):
- Handling Fee
- Total Goods
- Total Duty  
- Total Tax
- Total Before Shipping
- [Box size selector with shipping options]

**Wanted layout:**
- Total Goods
- Duty & Tax (combined: totalDuty + totalTax)
- *(divider)*
- Handling Fee & Shipping (combined: handlingFee + shippingBoxes[selectedBox].price)
- *(divider)*  
- Grand Total (grandTotal + shippingBoxes[selectedBox].price)

**How to do this:** User needs to upload the calculator HTML file into the chat. Claude will edit it and produce a complete replacement file. The box size selector section below the totals stays for now — that's a separate task.

**Note:** User also wants flat-rate shipping by country eventually (Europe £20, USA £30, Rest of World £40) and to remove box size selector — but that's a future session.

---

## Firebase structure

```
swis-9b0ce (Firebase project, europe-west1)
├── shipping-orders/
│   └── {000001, 000002...}/     ← keyed by order number
│       ├── orderNumber           ← "000001" etc.
│       ├── timestamp, status, staffMember, dutyPaid
│       ├── trackingNumber, receiptUrl, additionalNotes
│       ├── customer/ (name, email, phone, vatEori, address/)
│       ├── calculation/ (country, totalGoods, totalDuty, totalTax,
│       │                  handlingFee, grandTotal, shippingCost, boxSize)
│       └── itemDetails[] (description, composition, origin)
│
├── shipping-orders-archive/     ← same structure + archivedAt field
│
└── order-id-counter             ← integer, auto-incremented on each submit
```

---

## GitHub repo
https://github.com/jkconnollyengland-stack/connolly-calculator

Files:
- `connolly-calculator-with-admin.html` — main calculator (needs totals update)
- `orders-dashboard.html` — warehouse dashboard ✅
- `submit-order.html` — order submission form ✅
- `PROGRESS.md` — this file

---

## Passwords
- Calculator admin: `connolly2025`
- Orders dashboard: `Orders4!` (changeable in-app)

---

## User notes
- Jem, 61, non-technical
- GitHub method: open file → pencil icon → edit → Commit changes
- For uploading new files: drag file into chat → Claude edits → download → replace on GitHub
- Hard refresh after upload: Ctrl+Shift+R
- Prefers receiving complete replacement files over find-and-replace instructions

## Future tasks (not yet started)
- Flat-rate shipping by country: Europe £20, USA £30, Rest of World £40
- Remove box size selector from calculator
- These two go together — one task for a future session
