# Fashion Sales Dataset — Full Analysis

## 1. Dataset Properties

### What It Covers

The dataset records **monthly sales of a fashion retailer** over a two-year period — **January 2024 through December 2025**. It tracks five product categories sold through two channels.

### How It Is Organized

Each row is one **month × category × channel** combination. Because there are:

- **24 months** (Jan 2024 – Dec 2025)
- **5 categories** (Dresses, Denim, Outerwear, Footwear, Accessories)
- **2 channels** (Online, Store)

…the dataset has **24 × 5 × 2 = 240 rows** (plus one header). Each row contains all the sales metrics for that specific slice of the business.

### Column-by-Column Guide

| Column | What It Means | Notes |
|---|---|---|
| **month** | Label like `2024-01` (January 2024) | Derived from `year` + `month_num` |
| **year** | 2024 or 2025 | — |
| **month_num** | 1 through 12 | — |
| **category** | Product type: Dresses, Denim, Outerwear, Footwear, Accessories | — |
| **channel** | Sales channel: Online or Store | — |
| **avg_temp_F** | Average monthly temperature in Fahrenheit | Same for both channels in the same month |
| **list_price** | Manufacturer's suggested / original price per unit | Varies by category (e.g., Accessories = $35, Outerwear = $180) |
| **discount_pct** | Stated discount percentage applied (0–40%) | — |
| **sale_price** | Actual price the customer paid per unit | **NOT** a simple function of list_price and discount_pct (see below) |
| **ad_spend** | Advertising dollars spent on that category/channel/month | — |
| **avg_rating** | Average customer rating out of 5 | — |
| **units_sold** | Number of units sold | — |
| **revenue** | Total revenue generated | **Exactly** `sale_price × units_sold` |
| **return_rate** | Fraction of items returned (0.010 = 1.0%, 0.356 = 35.6%) | — |

### Which Columns Depend on Each Other

```
month          =  derived from year + month_num  ✓ (0 mismatches)
revenue        =  sale_price × units_sold        ✓ (0 mismatches)
sale_price     =  list_price × (1 − discount_pct)  ✗ (239 of 240 rows differ)
```

The **surprising discovery** is that `sale_price` almost never equals `list_price × (1 − discount_pct/100)`. The effective discount (computed from actual sale price) differs from the stated `discount_pct` in both directions — sometimes the actual price is higher than expected, sometimes lower. This means either:

- There is a **second discount or pricing mechanism** not captured in the data (e.g., promotions, clearance markdowns, loyalty discounts, or vendor allowances)
- The `discount_pct` field records the *planned* or *advertised* discount, not the final applied one
- Some rows combine multiple transactions at different discount levels

This is the most important data quality caveat in the dataset.

### Independent Columns (no dual from others)

The following columns are **genuinely independent inputs** — they are not calculated from other columns:

- `category`
- `channel`
- `list_price`
- `discount_pct`
- `ad_spend`
- `avg_temp_F`
- `avg_rating`

Everything else is either derived or a consequence of these plus `units_sold`.

---

## 2. Summary Statistics

| Column | Min | Max | Mean | Median |
|---|---|---|---|---|
| Avg Temperature (°F) | 31.3 | 80.4 | 55.6 | 56.2 |
| List Price ($) | 35 | 180 | 96.0 | 85 |
| Discount (%) | 0 | 40 | 22.6 | 25 |
| Sale Price ($) | 19.57 | 197.18 | 73.71 | 68.31 |
| Ad Spend ($) | 2,050 | 19,270 | 9,740 | 9,430 |
| Avg Rating | 3.4 | 5.0 | 4.2 | 4.2 |
| Units Sold | 220 | 2,488 | 844 | 820 |
| Revenue ($) | 17,257 | 154,513 | 55,085 | 48,838 |
| Return Rate | 1.0% | 35.6% | 17.3% | 17.5% |

### Overall Business Scale

| Metric | Total (all 240 rows) |
|---|---|
| **Total revenue** | **$13,220,482** |
| **Total units sold** | **202,500** |
| **Total ad spend** | **$2,337,530** |
| **Ad spend as % of revenue** | **17.7%** |
| **Revenue per $1 of ad** | **$5.66** |

---

## 3. Seasonal Patterns

**Revenue is highest in Winter and Fall**, driven by Outerwear:

| Season | Total Revenue | Revenue/Row |
|---|---|---|
| Winter (Dec–Feb) | $3,511K | $58,518 |
| Fall (Sep–Nov) | $3,477K | $57,954 |
| Spring (Mar–May) | $3,131K | $52,180 |
| Summer (Jun–Aug) | $3,101K | $51,690 |

Each category has very different seasonality:

| Category | Peak Month | Trough Month | Swing |
|---|---|---|---|
| Dresses | **July** (2,160 avg) | February (479) | **4.5×** |
| Outerwear | **December** (1,243) | June (312) | **4.0×** |
| Footwear | **December** (1,185) | April (497) | 2.4× |
| Denim | **December** (1,184) | April (639) | 1.9× |
| Accessories | **December** (1,432) | February (811) | 1.8× |

- **Dresses are the most seasonal category** — summer sells 4.5× more than winter.
- **Outerwear is the inverse** — winter sells 4× more than summer.
- **Denim and Accessories are steady performers** — less than 2× variation all year.

---

## 4. 2024 vs 2025 — Year-over-Year Growth

The business grew across the board:

| Metric | 2024 | 2025 | Change |
|---|---|---|---|
| Revenue | $6,407K | $6,814K | **+6.3%** |
| Units sold | 97,712 | 104,788 | **+7.2%** |
| Avg sale price | $73.54 | $73.87 | flat |
| Avg discount | 22.9% | 22.2% | −0.7 pp |
| Avg ad spend | $9,152 | $10,328 | **+12.8%** |
| Avg rating | 4.24 | 4.20 | −0.04 |
| Return rate | 17.2% | 17.4% | flat |
| Avg units/row | 814 | 873 | **+7.2%** |

Growth by category:

| Category | 2024 Rev | 2025 Rev | Change |
|---|---|---|---|
| **Dresses** | $1,435K | $1,560K | **+8.7%** |
| Accessories | $632K | $674K | +6.7% |
| Denim | $1,009K | $1,070K | +6.1% |
| Outerwear | $1,969K | $2,077K | +5.5% |
| Footwear | $1,362K | $1,432K | +5.1% |

Key takeaway: The business invested 13% more in advertising and got ~6–7% more revenue back. Since revenue growth was slightly behind ad spend growth, the marginal return on the extra ad dollars was lower than the average — consistent with diminishing returns.

---

## 5. Temperature Effects

Temperature correlates strongly with two categories and weakly with the others:

| Category | Correlation (r) | Interpretation |
|---|---|---|
| **Outerwear** | **−0.771** | Strong negative — sells far more in cold weather |
| **Dresses** | **+0.621** | Strong positive — sells far more in warm weather |
| Denim | −0.274 | Weak negative (slightly better in cold) |
| Accessories | −0.251 | Weak negative |
| Footwear | −0.215 | Weak negative |

Concrete numbers:

| Temp Bucket | Dresses | Outerwear |
|---|---|---|
| **Cold** (<40°F) | 677 avg units | **965** avg units |
| Cool (40–55°F) | 864 | **993** |
| Mild (55–70°F) | 910 | 532 |
| **Warm** (70°F+) | **1,427** | 352 |

- Dresses sell **2.1× more** in warm weather than cold.
- Outerwear sells **2.7× more** in cold weather than warm.

---

## 6. Discounts — Do They Drive Volume? **Yes.**

| Discount Tier | Avg Units Sold | Number of Rows |
|---|---|---|
| 0% | 586 | 43 |
| 1–9% | 607 | 12 |
| 10–19% | 714 | 25 |
| 20–29% | 791 | 73 |
| **30–40%** | **1,027** | 99 |

**30%+ discounts sell 75% more units than no discount.** The correlation is positive across every category:

| Category | Discount vs Units (r) |
|---|---|
| **Denim** | **+0.748** — most responsive |
| Footwear | +0.694 |
| Outerwear | +0.569 |
| Accessories | +0.552 |
| Dresses | +0.550 |

The effect holds through both channels:

- **Online**: 0% → 597 units, 30%+ → 1,113 units (+86%)
- **Store**: 0% → 567 units, 30%+ → 953 units (+68%)

⚠️ **Caveat**: Since `sale_price` doesn't cleanly follow from `list_price × (1 − discount_pct)`, the stated discounts may not be the full story. The deeper discount tiers likely coincide with other promotional levers.

---

## 7. Ad Spend — Diminishing Returns

| Ad Spend Tier | Avg Revenue | Revenue per $1 Ad |
|---|---|---|
| **<$5K** | $48,177 | **$13.74** 🔥 |
| $5–10K | $50,138 | $6.62 |
| $10–15K | $59,112 | $4.84 |
| **>$15K** | $66,282 | **$3.86** ⬇ |

The **first few thousand dollars are highly efficient**. Beyond $10K, returns drop sharply.

ROI by category:

| Category | Rev per $1 Ad | Total Ad Spend |
|---|---|---|
| **Outerwear** | **$8.52** 🥇 | $474,890 |
| Dresses | $6.57 | $456,100 |
| Footwear | $6.18 | $452,080 |
| Denim | $4.25 | $489,100 |
| Accessories | **$2.81** | $465,360 |

- **Outerwear ads have the best ROI** ($8.52 back per $1) — high price point makes each sale more valuable.
- **Accessories ads have the worst ROI** ($2.81) — low price point and heavy competition.
- **Store ads outperform online ads** — $6.27 vs $5.20 per $1 spent.

---

## 8. Returns — The Online Problem

The single dominant factor driving returns is **channel**, not category:

| Channel | Avg Return Rate |
|---|---|
| **Online** | **24.7%** |
| **Store** | **10.0%** |

Online returns are **2.5× higher**, and the gap is consistent across **every** category:

| Category | Online Return | Store Return | Gap |
|---|---|---|---|
| Footwear | 29.9% | 14.8% | 15.1 pp |
| Dresses | 28.9% | 14.8% | 14.1 pp |
| Denim | 22.3% | 6.7% | 15.6 pp |
| Accessories | 21.3% | 6.4% | 14.9 pp |
| Outerwear | 20.9% | 7.2% | 13.7 pp |

The gap is remarkably stable at **14–16 percentage points** — it's a structural problem with the online channel, not a category issue.

**Other factors linked to returns:**

| Factor | Correlation (r) | Meaning |
|---|---|---|
| **Rating** | **−0.353** | Lower-rated products have higher returns |
| Ad spend | +0.228 | More advertised = slightly more returns |
| Discount | −0.102 | Very weak — discounts barely affect returns |
| Temperature | −0.004 | No relationship |
| Sale price | +0.054 | No practical relationship |

**Rating has a real effect:**

| Rating Bucket | Avg Return Rate |
|---|---|
| 3.0–3.9 | **23.0%** |
| 4.0–4.4 | 16.8% |
| **4.5–5.0** | **13.8%** ✅ |

Products rated 4.5+ have **40% fewer returns** than those rated below 4.0.

The **worst rows** for returns are consistently **Footwear Online** (35.6%, 33%, 32.1%) and **Dresses Online** (34.2%). The **best rows** are **Denim Store** (1.0%) and **Accessories Store** (2.1%).

---

## 9. Ratings

Ratings are very consistent across categories (4.18–4.25), but differ by channel:

| Channel | Avg Rating |
|---|---|
| **Store** | **4.29** |
| **Online** | 4.15 |

The 0.14-point gap likely reflects the in-store experience — trying before buying, personal service, and no shipping hassle.

No other column meaningfully correlates with ratings:

| Factor | Correlation (r) |
|---|---|
| Return rate | −0.353 |
| Units sold | +0.132 |
| Ad spend | −0.085 |
| Temperature | +0.004 |
| Discount | −0.013 |
| Sale price | −0.014 |

The only notable relationship is the inverse link with **returns** — the same pattern seen above: better ratings → fewer returns.

---

## 10. Best and Worst Performing Segments

### Top 5 Revenue Rows

| Month | Category | Channel | Revenue | Units | Price | Discount |
|---|---|---|---|---|---|---|
| 2025-12 | Outerwear | Online | **$154,513** | 1,559 | $99.11 | 40% |
| 2024-11 | Outerwear | Online | **$150,604** | 1,372 | $109.77 | 40% |
| 2025-12 | Outerwear | Store | **$143,766** | 1,074 | $133.86 | 30% |
| 2024-12 | Outerwear | Online | **$136,768** | 997 | $137.18 | 30% |
| 2024-12 | Outerwear | Store | **$135,213** | 1,341 | $100.83 | 40% |

**All five are Outerwear in November/December** — the seasonal peak with strong discounts.

### Bottom 5 Revenue Rows

| Month | Category | Channel | Revenue |
|---|---|---|---|
| 2025-10 | Accessories | Online | $17,257 |
| 2025-05 | Accessories | Store | $17,940 |
| 2024-01 | Accessories | Store | $19,060 |
| 2025-06 | Accessories | Store | $19,236 |
| 2025-10 | Accessories | Online | $20,418 |

**All five are Accessories** — the lowest-priced category, making it hard to generate high revenue even with decent unit sales.

---

## 11. Surprising Things Worth Highlighting

1. **Discounted price ≠ expected price.** In 239 of 240 rows, `sale_price` does not equal `list_price × (1 − discount_pct/100)`. Nobody who designed this dataset made it that simple.

2. **Online vs Store is the most powerful split in the data.** It dominates returns (24.7% vs 10.0%), matters for ratings (4.15 vs 4.29), and influences ad ROI ($5.20 vs $6.27) — more than any category difference.

3. **Discounts do work, but not perfectly.** Deep discounts (30%+) sell 75% more units, yet the relationship between discount % and units sold is only moderately strong (r ≈ 0.55–0.75). Price cuts help, but they're not magic.

4. **Denim is the most discount-sensitive category** (r = 0.748) — people wait for sales on jeans.

5. **Accessories are the low-margin workhorse.** They sell the most units (49,086) but generate the least revenue ($1.3M) — a high-volume, low-value category.

6. **Ad spend has dramatic diminishing returns.** The jump from $5K to $15K+ more than doubles the spend but only adds ~$18K in average revenue, while ROI per dollar drops from $13.74 to $3.86.

7. **Every single category grew year-over-year.** Even during a challenging retail period (2024–2025), this business grew 6.3% in revenue and 7.2% in unit volume.

8. **Returns are predictable.** If you know the channel and rating, you can predict return rate reasonably well. Online + low rating = trouble. Store + high rating = safe.

---

*Analysis generated from fashion_sales.csv — 240 rows, 14 columns covering January 2024 through December 2025.*