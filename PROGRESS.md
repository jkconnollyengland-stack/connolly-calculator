# Session Progress Notes

## What the app is
Connolly Duty, Tax and Shipping Calculator
Main file: connolly-calculator-with-admin.html
Admin password: connolly2025

## What was done in recent sessions
- Added Connolly logo (white on navy #1a2332 background)
- Logo file: CB Logo for app.jpg (URL encoded as %20 in link)
- Replaced global flat-rate postage override with per-country shipping rates
- Each country now has its own shipping rate field in admin panel
- Shipping rate stored as `shipping` field on each data row
- If shipping != null for a country, all boxes show that price; otherwise uses individual box prices

## Data structure
INITIAL_DATA: array of objects, one per country+category combination
Fields: country, category, hts, F (duty), G (tax), H (handling), boxes (array of 5 prices), shipping (flat rate or null), T (threshold)
Countries: Australia, Argentina, Brazil, Canada, China (and more)
Categories: FASHION, LEATHER GOODS, ANTIQUES, ART, HOMEWARES

## Key code locations
- `const [handlingFeeOverride, setHandlingFeeOverride] = useState(null);` — global handling fee override (still active)
- Shipping logic: reads `firstRow.shipping` — if not null, uses it for all boxes
- saveEdit(country, category, field, value) — saves edits to localStorage
- Admin section uses `countries.map(c => ...)` to render per-country shipping inputs

## What to work on next
- (Update this when you know what's next!)

## How to start a new session
Share the raw file link:
https://raw.githubusercontent.com/jkconnollyengland-stack/connolly-calculator/refs/heads/main/connolly-calculator-with-admin.html
Tell Claude to read PROGRESS.md first, then fetch the file.
