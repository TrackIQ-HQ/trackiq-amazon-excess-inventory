# Method

## The arithmetic

```
daily_units   = units in 90-day window / 90
cover         = on_hand / daily_units          # days. infinite if daily_units == 0
excess_units  = max(0, on_hand - daily_units x target_cover)
capital       = excess_units x unit_cost       # or x price, labelled revenue value
```

`target_cover` defaults to **90 days**. Anything beyond that is capital doing
nothing. Use the client's figure if they have one — a brand with a 120-day lead
time legitimately holds more.

## The bands

| Band | Cover | Reading |
|---|---|---|
| **Healthy** | < 120 days | nothing to do |
| **Heavy** | 120–180 days | watch; a small discount or ad push clears it |
| **Excess** | 180–365 days | act this quarter |
| **Dead** | > 365 days, or no sales in 90 days | act now |
| **Stranded** | SKU cover > 365 while the ASIN is healthy | the stock is on the wrong SKU |

**Stranded is the interesting one** and only visible per SKU. The ASIN sells
fine; one of its SKUs holds thousands of units nobody is buying. No ASIN-level
report will ever show it.

## Before any recommendation: three disqualifiers

Check all three. Each turns a liquidation candidate into something else
entirely.

**1. Seasonality.** Compare the same 90 days a year earlier. If units were
similarly low then and high in another quarter, this is an off-season product.
**Recommend holding** and say when the season starts. Getting this wrong
destroys real money.

**2. Visibility.** Zero sales with zero sessions and zero ad spend is not proof
of no demand — it is proof nobody has been able to find it. Recommend a
visibility check before liquidation.

**3. Buyability.** A suppressed listing, a lost buy box, or a long
`out_of_stock` spell sells nothing regardless of demand. Run
`trackiq-listing-monitor` first.

A report that liquidates a seasonal product in its off season, or a product that
was suppressed for two months, is worse than no report.

## The five recommendations

One per product. Not a menu.

| Recommendation | When | The action |
|---|---|---|
| **Hold** | seasonal, off season | nothing until the season; say which month |
| **Advertise** | healthy conversion, low sessions, excess stock | spend, do not discount — the demand exists and is not being reached |
| **Discount** | decent traffic, weak conversion, excess stock | price or coupon; give a target that clears it inside the target cover |
| **Bundle** | slow SKU beside a fast sibling | a multipack or a variation pairing |
| **Remove** | dead, unfulfillable, or discontinued | removal order or disposal |

**Advertise before discount, always, where conversion is healthy.** Discounting a
product that converts well and gets no traffic destroys margin to solve a
problem that was never about price. This is the most common mistake in an excess
report and it is expensive.

### Sizing a discount

```
needed_daily = excess_units / target_clear_days           # default 90
uplift       = needed_daily / daily_units
```

Price elasticity is not in this data, so the discount percentage is a
**judgement, not a calculation**. State the required uplift and let the client's
merchandiser choose the price. Never present a computed discount percentage as
if it were derived — it would be a fabrication dressed as arithmetic.

If `uplift` is above about 3x, say plainly that no realistic discount clears
this inside the window and the honest options are removal or a long hold.

## Unfulfillable — a separate section

```
unfulfillable_units, unfulfillable_value = units x unit_cost
```

Already lost. Report the total, list the worst SKUs, and give three options:
removal order, disposal, or investigation where a SKU shows an unusual
concentration (which usually means a packaging or a carrier problem worth
fixing).

Never discount unfulfillable stock — it cannot be sold. Never blend it into the
excess totals; it makes healthy products look overstocked.

## Ranking

**By capital tied up**, descending. Ten thousand units of a $2 item is $20,000;
four hundred of a $40 item is $16,000 — and the second is usually the easier
problem to solve. Show both units and capital so the reader can see which they
are looking at.

If unit cost was unavailable, rank by retail value and **label every total
revenue value** throughout — in the KPI tile, the column header and the summary
sentence, not once in a footnote.

## What this skill does not do

- **No inventory age.** Not in the data. No storage-fee or surcharge exposure.
- **No price elasticity**, so no computed discount percentage.
- **No removal orders.** Removals cannot be undone; a human decides.
- **No forecast.** Velocity is a trailing 90-day average with a year-ago
  seasonality check, not a projection.
