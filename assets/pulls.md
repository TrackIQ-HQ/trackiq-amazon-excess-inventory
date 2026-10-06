# The pull sequence

## 0. Account and unit cost

`list_marketplaces` first. Never print `account_id`.

**Ask for unit cost.** Landed cost per unit, by SKU if they have it, or an
average if not.

Without it, value stock at selling price and label every total **revenue
value**, not capital. The difference matters: a client told they have $340,000
"tied up" when the figure is retail value will make a decision on a number
roughly three times too big.

## 1. Inventory

```
get_inventory_snapshot(account_id, limit=100, offset=…)
```

**Paginate** — caps at 100 rows.

Fields: `on_hand`, `inbound`, `reserved`, `unfulfillable`, `researching`,
`total`, `out_of_stock`, `asin`, `sku`, `title`.

## 2. What is not here, and it is the point

**There is no age.** No received date, no age buckets, no days-in-warehouse, no
storage-fee field, no aged-inventory surcharge figure.

So this skill **cannot** compute:

- long-term storage fee exposure
- aged-inventory surcharge liability
- how long any particular unit has been sitting

Say this on the report, near the top, in plain words. A client who has been told
about the 271-day surcharge will look for it, and a report that silently omits
it reads as if it were checked and found clear.

What this skill measures instead is **cover** — stock divided by the rate it
sells at. That is a good proxy for excess and a genuinely better basis for the
decision, because it is forward-looking. It is not the same thing as age, and
the report should not imply it is.

If the client needs true age, it is in Seller Central's Inventory Age report or
the FBA Inventory Ledger. Say so and offer to work from an export.

## 3. Velocity — 90 days, not 30

```
get_product_performance(account_id, start_date=<90 days ago>, end_date=<yesterday>,
                        group_by='product', limit=200)
```

**Ninety days.** A 30-day window declares every off-season product dead. Cough
drops in June and sunscreen in December both look like liquidation candidates on
a month of data and are not.

Gives `units`, `revenue`, `sessions`, `orders` per SKU. Derive price as
`revenue / units`; there is no price field.

Pull the **same 90 days a year earlier** as well where it exists. It is the only
cheap seasonality check available, and it turns "this has not sold since March"
into "this never sells between March and August". Note that ad fields a year
back may be zero on accounts connected more recently — revenue and units are
still valid.

## 4. Both levels, kept distinct

This is the one skill in the set that works **per SKU** as well as per ASIN, and
the distinction has to be explicit on the page.

```
ASIN level  — is the product overstocked?     sum stock, sum units, then divide
SKU level   — is the stock on the right SKU?  per-SKU cover, shown underneath
```

The stranded case is real and only visible per SKU: one SKU holding thousands of
units while a sibling SKU on the same ASIN sells everything. The ASIN looks fine.
The capital is still stuck.

Label every cover figure with which level it came from. Mixing them is the trap
that `trackiq-restock-priority` documents from the other direction.

## 5. Is it excess, or is it invisible?

```
get_product_ads(account_id, start_date, end_date, limit=500)
```

Filter to the candidate ASINs. A product with zero sales **and** zero ad spend
**and** low sessions is not proven dead — nobody has been able to find it.

Check before recommending liquidation:

- sessions in the window — is there any traffic at all?
- ad spend — has it been supported?
- `out_of_stock` flag history — was it even buyable?

A listing that is suppressed or out of the buy box sells nothing regardless of
demand. `trackiq-listing-monitor` answers that; run it before writing anything
off.

## 6. Unfulfillable — its own list

`unfulfillable` units are damaged, expired or customer-returned-unsellable.
They are already lost. Total them across the catalogue and list the worst SKUs.

The decisions there are remove, dispose or investigate — never discount. Keep
them out of the excess arithmetic entirely; blending them in makes healthy
products look overstocked.
