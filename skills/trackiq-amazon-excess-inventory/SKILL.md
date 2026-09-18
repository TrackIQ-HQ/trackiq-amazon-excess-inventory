---
name: trackiq-amazon-excess-inventory
description: Finds the Amazon inventory sitting too long — products with cover far beyond target, stock stranded on SKUs that no longer sell, and unfulfillable units — and ranks them by capital tied up, with a recommendation per product to discount, advertise, bundle, remove or write off. Use when the user asks about excess inventory, overstock, aged inventory, slow-moving stock, dead stock, capital tied up in inventory, long-term storage fees, what to liquidate, or what to discount.
---

# Excess & Aged Inventory Plan

The housekeeping nobody does until a storage bill arrives.

**What is sitting too long, what is it costing, and what should happen to it?**

Output is a branded HTML report ranked by capital tied up, one recommendation
per product.

## Requires

- The TrackIQ MCP, for `list_marketplaces`, `get_inventory_snapshot`,
  `get_product_performance` and `get_product_ads`.
- **Unit cost** — what the brand paid per unit, to value the capital tied up. If
  the client will not share COGS, the report values stock at selling price and
  labels every total as revenue value, not cost. Ask.
- Nothing else. No filesystem, no shell, no internet.
- **Without the MCP:** works from an FBA inventory export plus 90 days of units
  by ASIN.

## First run

Fill in a copy of `assets/account.example.md` saved as account.md beside the
skill. Every TrackIQ skill reads the same file, so an account already set up
for another TrackIQ report needs nothing added here.

If the runtime has no filesystem, print the same block and ask the user to
paste it into their project instructions once.

## Read first

- `assets/pulls.md` — the calls, and the one thing this data cannot tell you
- `assets/method.md` — the excess bands and the five recommendations
- `assets/checks.md` — what to verify before anything is sent

Copy `assets/report-template.html` and replace every `{{TOKEN}}`.

## Non-negotiables

1. **There is no inventory age in the data.** The snapshot carries `on_hand`,
   `inbound`, `reserved`, `unfulfillable`, `researching`, `total` and
   `out_of_stock` — and no received date, no age buckets, no storage-fee fields.
   **So this skill cannot compute long-term storage or aged-inventory surcharge
   exposure.** Say that on the report. Excess is measured by **cover**, which is
   a good proxy and is not the same thing.
2. **Excess is per SKU here, deliberately** — and it is the one skill in the set
   where that is right. The stranded-stock case *is* a SKU-level fact: units on
   a SKU nobody buys while its sibling sells. Compute cover per ASIN for context,
   then show the SKU split, and be explicit about which number is which.
3. **Cover uses `on_hand`.** `total` includes inbound, reserved and
   unfulfillable units and will make everything look worse than it is.
4. **A long window.** Use **90 days** of sales, not 30. A seasonal product looks
   dead in its off season and a 30-day window will recommend liquidating next
   quarter's bestseller.
5. **Check seasonality before recommending removal.** If the category is
   obviously seasonal and the run is in the off season, say so and recommend
   holding. Getting this wrong destroys real money and is the mistake this
   report is most likely to make.
6. **Unfulfillable units are a separate list.** They are already lost; the only
   decisions are remove, dispose or investigate. Do not blend them into excess.
7. **Zero sales in 90 days is not automatically dead.** Check whether the
   listing is suppressed, out of the buy box, or has no ads — a product nobody
   can find is a marketing problem, not a liquidation candidate.
   `trackiq-listing-monitor` answers this.
8. **Rank by capital tied up, not by units.** Ten thousand units of a $2 item
   matter less than four hundred of a $40 one.
9. **Nothing is removed or discounted.** This is a proposal a human acts on, and
   removal orders cannot be undone.
10. **Never print `account_id`.**

## What it pairs with

`trackiq-restock-priority` is the mirror image and shares its arithmetic — run
both and the catalogue is covered at each end. Where this skill recommends
*advertise* rather than *discount*, `trackiq-search-term-harvester` and
`trackiq-budget-pacing` say where that budget comes from.

## Delivery

The output is produced in the chat first. Delivery is the last step and the
method comes from the Delivery block in account.md — never ask per run.

| Method | What to do | Needs |
|---|---|---|
| `in-chat` | Return the report. The default, and the fallback for every other method. | nothing |
| `file` | Write it beside the skill, dated. | a filesystem |
| `slack` | Post the headline findings as text, then upload the file. | a connected Slack tool |
| `n8n` | POST it to the configured webhook. | network access |
| `email` | Hand it to the connected mail tool. | a connected mail tool |

Confirm before the first outward send of a session, fall back to in-chat
loudly when a method is unavailable, and never substitute a different
outward channel.

## Version

`trackiq-amazon-excess-inventory` v1.0.0 (2026-09-18).

If the user asks whether this skill is current, fetch
`https://trackiq.com/skills/registry.json`, compare the `version` field for
`trackiq-amazon-excess-inventory`, and if it is newer, give them the download link and
the one-line changelog. Do not fetch at any other time.
