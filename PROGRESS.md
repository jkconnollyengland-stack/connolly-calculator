# Connolly App — Session Handover
**Date:** 6 May 2026
**Previous session:** 16 April 2026

---

## Files and their current state

### connolly-calculator-with-admin.html 🔄 IN PROGRESS

**✅ Comprehensive duty and tax rate review (NEW — 6 May 2026)**

Researched current duty and tax rates for all 20 destination countries against multiple authoritative sources. Key updates committed:

- **Switzerland** VAT corrected from 7.7% → 8.1% (rose 1 January 2024 under AVS 21 reform)
- **Australia** Fashion tax corrected from 20% → 10% GST (was a typo; all other Australia rows already correct at 10%)
- **Singapore** Leather Goods and Homewares tax corrected from 7% → 9% (Singapore GST rose to 9% on 1 January 2024)
- **Brazil** tax raised to 50% across all 5 categories. Brazil's real burden is II + IPI + PIS/COFINS + ICMS stacked, totalling 60–80% of CIF in practice. The 50% figure is a working estimate; a customer-facing notice now appears when Brazil is selected, recommending DAP shipping
- **Canada** tax set to flat 13% across all categories (covers most provinces under HST; previously was 5% federal GST only, undercharging by 8–10% in most provinces)
- **United States** rebuilt with realistic MFN base duties (Fashion 18%, Leather/Homewares 10%, Antiques/Art 0%) and tax dropped to 5% buffer for state sales tax exposure

**✅ US Tariff Surcharge multiplier (NEW — 6 May 2026)**

Added a separate admin-adjustable multiplier for US tariff volatility, modelled on the existing shipping surcharge multiplier.

- Default value: **1.15** (15% Section 122 tariff currently active)
- Applied ON TOP of US base MFN duty rates only; other countries unaffected
- Persists in `localStorage` (key: `connolly-us-tariff-surcharge`)
- "Reset to 1.15" button when changed
- Status display shows current surcharge percentage
- **Why this matters:** Trump's IEEPA reciprocal tariffs were vacated by SCOTUS on 20 February 2026 and replaced with a Section 122 tariff that is time-limited to 150 days, expiring around **24 July 2026** unless extended. The dial lets Jem react without code changes.

**✅ Brazil customer advisory notice (NEW — 6 May 2026)**

Small amber notice appears between country selector and items section when Brazil is the destination. Explains the tax stack and recommends offering DAP shipping so the customer handles local clearance.

**✅ Bug fix: Per-Country Shipping Rate orphan code (RESOLVED — 6 May 2026)**

The `postageOverride` orphan code in the Per-Country Flat Rate Postage section had been flagged as fixed in the previous session but was still present in the live file. Cleaned up properly this session — admin panel now renders without latent runtime errors.

**Still to do on the calculator:**
- Restructure the totals display section (see layout below)
- Flat-rate shipping by country: Europe £20, USA £30, Rest of World £40
- Remove box size selector from calculator
- These last two go together — one task for a future session

**Wanted totals layout:**
- Total Goods
- Duty & Tax (combined: totalDuty + totalTax)
- *(divider)*
- Handling Fee & Shipping (combined: handlingFee + shippingBoxes[selectedBox].price)
- *(divider)*
- Grand Total (grandTotal + shippingBoxes[selectedBox].price)

### orders-dashboard.html ✅ COMPLETE
All features done and working:
- Password protection (default: `Orders4!`, changeable in-app, stored in localStorage)
- Lock button clears session
- **Search** — real-time filter across name, email, phone, address, country, staff, tracking. Highlights matches in gold.
- **Archive tab** — Archive button per order, confirmation dialog, moves to `shipping-orders-archive` in Firebase. Restore button brings it back.
- **Delete button** — red 🗑️ button on every order (active and archived), confirmation dialog, permanent delete from Firebase
- **Print** — prints single order cleanly, white background, hides buttons
- Status workflow: Pending → Processing → Completed → Reopen
- Order number display: shows `Order #000001` (falls back to truncated Firebase key for old orders)

### submit-order.html ✅ COMPLETE
- Sequential order IDs: reads/increments `order-id-counter` in Firebase using atomic transaction
- Order saved as `shipping-orders/000001` etc. (number is also stored as `orderNumber` field)
- Success screen shows `Order #000001` in a gold box
- Everything else unchanged from previous version

---

## Admin Panel — current sections (top to bottom)

1. **💷 Global Handling Fee** — sets one handling fee for all countries
2. **🚢 Shipping Surcharge Multiplier** — default 1.3 (30% surcharge); scales all box shipping prices. Used for geopolitical shipping surcharges (e.g. Iran-related rate changes)
3. **🇺🇸 US Tariff Surcharge** — default 1.15 (15% surcharge); applied on top of US base MFN duty only. Used for Trump-era tariff volatility (Section 122, etc.)
4. **📦 Flat Rate Postage by Country** — per-country flat shipping rate override
5. **Country selector + rates editor** — edit individual F/G/H rates per country/category

Two `localStorage` keys store admin-adjustable surcharges:
- `connolly-surcharge-multiplier` (shipping surcharge, default 1.3)
- `connolly-us-tariff-surcharge` (US tariff surcharge, default 1.15)

---

## US Tariff Situation (as of 6 May 2026)

The US duty regime has been turbulent. Brief timeline:

- **April 2025** — Trump introduced IEEPA "reciprocal" tariffs (10% baseline, higher per country)
- **August 2025** — $800 de minimis exemption eliminated; every parcel now pays duty regardless of size
- **February 20, 2026** — SCOTUS ruled IEEPA does not authorise the President to impose tariffs; reciprocal tariffs vacated
- **February 24, 2026** — Trump replaced them with a Section 122 tariff (15%, time-limited 150 days)
- **Around 24 July 2026** — Section 122 expires unless Congress extends it

**Action required around 20 July 2026:** Jem to check the US tariff status and adjust the US Tariff Surcharge multiplier in the Admin Panel. Set to 1.0 if the surcharge has expired and not been replaced, or to whatever rate the new regime imposes.

---

## Spreadsheet Reference (APP_FINAL_CUT_SPREADSHEET.xlsx)

Understanding the shipping surcharge formula:
- **Columns O–S** ("BOX SIZE LESS SURCHARGE") = base shipping prices (hardcoded values)
- **Columns J–N** ("BOX SIZE INC SURCHARGE") = `=O*1.3`, `=P*1.3`, etc. — base price × 1.3 (30% surcharge)
- Row 7 = header row, Row 8+ = data rows
- Two sheets: "SELECTION OF COUNTRIES" (standard shipping) and "EXPRESS AIR EU"
- The shipping surcharge multiplier in the app mirrors this spreadsheet behaviour

**Note:** the spreadsheet's duty and tax rates are now out of date relative to the calculator. The calculator's `INITIAL_DATA` is the source of truth following the 6 May 2026 review. If the spreadsheet is ever regenerated, refer to the calculator first.

---

## Origin assumptions (rules of origin)

Goods ship FROM the UK but are mostly of Italian/EU origin. This means:
- UK FTAs with Japan, Australia, Singapore, Switzerland, Norway, Canada, etc. **do not apply** because the goods aren't UK-origin under rules of origin
- MFN (Most Favoured Nation) rates are used throughout the calculator
- This is generally what Connolly will actually pay; no preferential rates assumed

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
- `connolly-calculator-with-admin.html` — main calculator (rate review complete, US surcharge added, totals restructure still pending)
- `orders-dashboard.html` — warehouse dashboard ✅
- `submit-order.html` — order submission form ✅
- `PROGRESS.md` — this file
- `APP_FINAL_CUT_SPREADSHEET.xlsx` — source spreadsheet (rates partially superseded by 6 May 2026 review)
- `CB_Logo_for_app.jpg` — Connolly brand logo

**Important note for future sessions:** the file uploaded to the Claude project knowledge can lag behind the live GitHub copy. Always check that the project knowledge calculator file matches what's actually live before making changes. If unclear, fetch the latest live version from GitHub history first.

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
- After updating INITIAL_DATA, click Admin Panel → 🔄 Reset to Original to clear cached old rates from localStorage
- Prefers receiving complete replacement files over find-and-replace instructions

---

## Future tasks (not yet started)
- Restructure totals display section (see layout above)
- Flat-rate shipping by country: Europe £20, USA £30, Rest of World £40
- Remove box size selector from calculator
- These last two go together — one task for a future session

## Things to revisit periodically
- **20 July 2026** — check US Section 122 status, adjust US Tariff Surcharge multiplier
- Quarterly check on VAT/GST rate changes globally (e.g. Switzerland VAT increase to 8.8% has been delayed to 2028 but could move)
- After any new HTS code subcategories Jem starts shipping (e.g. women's silk dresses 6204.43, different code with different duty)
