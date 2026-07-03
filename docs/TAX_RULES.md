# Tax Rules

This document describes all Indian capital gains tax rules encoded in TaxSetu's computation engine (`processedTransactions` and `stats` useMemo hooks).

---

## 1. Holding Period Classification

The app computes `holdingDays = days between buyDate and sellDate` and classifies gains as **STCG** (Short Term Capital Gain) or **LTCG** (Long Term Capital Gain) based on asset type.

| Asset Type | LTCG Threshold | STCG (< threshold) |
|-----------|----------------|-------------------|
| `equity` | ≥ 365 days | < 365 days |
| `debt_mf` | N/A (always STCG) | Any holding period |
| `gold_unlisted` | ≥ 730 days | < 730 days |
| `real_estate` | ≥ 730 days | < 730 days |

### Financial Year Determination

The function `getFinancialYear(dateStr)` maps an ISO date to an Indian financial year string `"YYYY-YY"` using the rule: a financial year runs **April 1 to March 31**.

- If month ≥ 3 (April onwards): FY = `year`-`year+1` (e.g. 2025-10-20 → "2025-26")
- If month < 3 (Jan-Mar): FY = `year-1`-`year` (e.g. 2025-02-15 → "2024-25")

> Note: JavaScript months are 0-indexed, so month 3 = April.

---

## 2. Tax Rates by Asset Type & Classification

### Equity (Listed Shares & Equity-Oriented MF)

| Classification | Tax Rate | Notes |
|---------------|----------|-------|
| LTCG (≥ 365 days) | **12.5%** | Flat rate, post-Budget 2024 |
| STCG (< 365 days) | **20%** | Flat rate |

**Section 112A Exemption**: First ₹1,25,000 of equity LTCG per financial year is tax-free. This is applied at the FY aggregation level (`stats`), not per-transaction.

### Debt Mutual Funds

| Classification | Tax Rate |
|---------------|----------|
| Any holding period | **User's income tax slab rate** (10%, 15%, 20%, 30%, or 39%) |

Debt MF gains are always added to the user's income and taxed at their applicable slab rate. No indexation, no special rate.

### Gold & Unlisted Shares

| Classification | Tax Rate | Notes |
|---------------|----------|-------|
| LTCG (≥ 730 days) | **12.5%** | Flat rate |
| STCG (< 730 days) | **User's slab rate** | Added to income |

### Real Estate

| Classification | Tax Rate | Notes |
|---------------|----------|-------|
| LTCG (≥ 730 days) | **12.5%** flat **OR 20% with indexation** | See Section 3 below |
| STCG (< 730 days) | **User's slab rate** | Added to income |

---

## 3. Real Estate Indexation (Dual Option)

For real estate LTCG where `buyDate < 2024-07-23` (pre-Budget 2024), the user has a choice between two regimes:

### Option A: 12.5% Flat (No Indexation)

```
tax = absoluteGain × 0.125
```

### Option B: 20% with CII Indexation

```
indexedCost     = buyCost × (CII[sellFY] / CII[buyFY])
indexedGain     = saleValue - indexedCost
tax             = indexedGain × 0.20
indexedAdvantage = tax(Option B) - tax(Option A)
```

The app automatically computes both options and selects the **lower tax amount**. If indexation yields a lower tax, a `taxOptimizationNote` is generated explaining the savings.

**Real estate bought on or after 2024-07-23**: Only Option A (12.5% flat) is available. The `holdsIndexedOption` flag is set to `false`.

### CII Table

The Cost Inflation Index is hardcoded from FY 2001-02 (index 100) through FY 2026-27 (index 390). See [`DATA_MODEL.md`](./DATA_MODEL.md#cii-table-cost-inflation-index) for the full table.

---

## 4. Section 112A Exemption (Equity LTCG)

Applied in the `stats` aggregation:

```
equityTaxExemptGains = min(totalEquityLTCG, 125000)
adjustedTax = totalTax - (equityTaxExemptGains × 0.125)
```

Only equity LTCG qualifies. The exemption is ₹1,25,000 per financial year total (not per transaction or per scrip).

### Tax Planner Display

The **Tax Planner** tab shows:
- **Exemption Used**: How much of the ₹1.25L exemption has been consumed by current FY's equity LTCG
- **Remaining Headroom**: `125000 - equityTaxExemptGains` — how much additional equity LTCG can be realized tax-free
- **Harvesting Opportunity**: If there are equity holdings with unrealized LTCG sitting near the threshold, the app highlights the headroom available

---

## 5. Computation Summary Flow

```
For each transaction:
  1. Compute holdingDays, buyCost, saleValue, absoluteGain
  2. Classify STCG/LTCG based on assetType + holdingDays
  3. Assign taxRate based on classification + assetType
  4. If real_estate + LTCG + buyDate < 2024-07-23:
     a. Compute indexedCost using CII ratio
     b. Compare 12.5% flat vs 20% indexed
     c. Pick lower tax, set indexationAdvantage
  5. calculatedTax = gains × taxRate (before 112A exemption)

For selected FY (stats aggregation):
  1. Filter transactions by sellFY == selectedFY
  2. Sum: totalEquityLTCG, totalOtherLTCG, totalSTCG, netGain
  3. Apply Section 112A: exemption = min(totalEquityLTCG, 125000)
  4. adjustedTax = totalTax - (exemption × 0.125)
```

---

## 6. Tax Slab Selector

The user can set their income tax bracket via a dropdown in the header. This affects:

| Slab Value | Label | Affects |
|-----------|-------|---------|
| 10 | 10% | Debt MF, gold/unlisted STCG, real estate STCG |
| 15 | 15% | Same |
| 20 | 20% | Same |
| 30 | 30% | Same |
| 39 | 39% (labeled "Surcharge") | Same — treated as flat rate |

> **Note**: The 39% option is labeled "39% Surcharge" in the UI but is treated as a flat 39% rate on applicable gains, not as a surcharge calculated on top of base tax.

---

## 7. Known Limitations

- No surcharge or health & education cess (4%) computed on top of base tax rates
- No grandfathering provisions (FMV as of January 31, 2018) for equity shares acquired before 2018
- No capital loss carry-forward or set-off logic across FYs
- Section 112A exemption is applied at FY level only; does not account for short-term gains or losses that could offset LTCG
- No distinction between resident and non-resident tax rates