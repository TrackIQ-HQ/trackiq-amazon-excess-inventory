# Before you send it

## 1. The age disclaimer

- **The report states near the top that inventory age is not available** in this
  data, and that excess is measured by cover instead.
- No storage fee, long-term storage or aged-inventory surcharge figure appears
  anywhere on the page.
- The Seller Central Inventory Age report is named as where true age lives.

## 2. The valuation

- Unit cost is stated, with whether it came from the client.
- If it did not, **every total says revenue value**, not capital — in the KPI
  tile, the column header and the summary sentence. Not once in a footnote.
- No figure mixes cost-valued and price-valued stock.

## 3. The window

- Velocity uses **90 days**, not 30.
- The year-earlier comparison was pulled where it exists.
- The window is stated on the report.

## 4. The three disqualifiers were applied

- **Seasonality checked** against the same period a year earlier. Any off-season
  product is recommended **hold**, with the month its season starts.
- **Visibility checked** — sessions and ad spend. Nothing with zero traffic and
  zero support is recommended for liquidation without saying it may be a
  findability problem.
- **Buyability checked** — suppression and buy box. `trackiq-listing-monitor` is
  named.
- Each disqualifier that fired is visible beside the product, so the reader can
  see why it was spared.

## 5. The two levels

- ASIN-level cover and SKU-level cover are **both labelled** with which they
  are.
- Stranded SKUs — high SKU cover on a healthy ASIN — have their own section.
- No figure silently mixes the two levels.

## 6. The recommendations

- **One per product.**
- **Advertise appears before discount** wherever conversion is healthy and
  sessions are low. Check the top five: is anything with good conversion being
  discounted?
- No discount **percentage** is computed. The required uplift is stated and the
  price is left to the client.
- Where the required uplift exceeds about 3x, the report says no realistic
  discount clears it.
- Nothing recommends discounting unfulfillable stock.

## 7. Unfulfillable

- In its **own section**, not in the excess totals.
- Total units and value are stated.
- A SKU with an unusual concentration is flagged for investigation, not just
  disposal.

## 8. The ranking

- Sorted by **capital tied up**, not units.
- Both units and capital are shown per row.

## 9. Sanity

- Total excess capital is plausible against the catalogue's annual revenue. If
  it exceeds a few months of sales, the target cover or the unit cost is wrong.
- No product with under 120 days of cover is in the action list.
- Nothing is being removed that sold in the last 30 days.

## 10. Render check

```js
({ overflows: document.documentElement.scrollWidth > document.documentElement.clientWidth,
   tables: document.querySelectorAll('table').length,
   rows: [...document.querySelectorAll('table')].map(t => t.querySelectorAll('tbody tr').length),
   logos: [...document.images].map(i => i.naturalWidth > 0),
   tokens: (document.body.innerHTML.match(/\{\{[A-Z0-9_]+\}\}/g) || []).length,
   // must be zero: this data cannot support any storage-fee claim
   storage: (document.body.innerText.match(/storage fee|surcharge|long-term storage/gi) || [])
              .filter(s => true).length })
```

`overflows` false, `logos` all true, `tokens` zero. `storage` should be 0, or 1
where the single sentence explaining the absence appears — anything more means a
fee claim leaked in. Then look at it; if it will not paint, say the check was
structural.

## 11. Ship

Save as `<client>-excess-inventory-<YYYY-MM-DD>.html`.

Lead with the capital tied up and the single largest item, then the one
recommendation you would act on this week. A list of forty products with no
first move does not get started.
