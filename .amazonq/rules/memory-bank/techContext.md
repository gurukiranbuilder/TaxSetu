# TaxSetu — Tech Context

## Runtime Stack
| Layer | Technology | Source |
|---|---|---|
| UI Framework | React 18 | unpkg CDN |
| JSX Transpiler | Babel Standalone | unpkg CDN |
| Styling | Tailwind CSS | cdn.tailwindcss.com |
| Icons | Lucide | unpkg CDN |
| Typography | Plus Jakarta Sans | Google Fonts |

## No Build Tooling
There is no package.json, no bundler (Webpack/Vite), no TypeScript, no linter config. Everything runs directly in the browser.

## Browser APIs Used
- `localStorage` — transaction persistence
- `ServiceWorker` + `Cache API` — offline support
- `URL.createObjectURL` + `Blob` — dynamic manifest and SW injection
- `window.matchMedia` — PWA standalone detection
- `navigator.standalone` — iOS PWA detection

## Data Model: Transaction Object
```js
{
  id: string,           // 'tx-' + Date.now()
  assetName: string,
  assetType: 'equity' | 'debt_mf' | 'gold_unlisted' | 'real_estate',
  buyDate: string,      // ISO date "YYYY-MM-DD"
  buyPrice: number,     // price per unit
  qty: number,
  sellDate: string,     // ISO date "YYYY-MM-DD"
  sellPrice: number,    // price per unit
}
```

## Processed Transaction (computed, not stored)
Extends the raw transaction with:
`holdingDays, buyFY, sellFY, buyCost, saleValue, absoluteGain, classification, taxRate, holdsIndexedOption, indexedCost, indexedGain, calculatedTax, indexationAdvantage, taxOptimizationNote`

## Tax Rules Encoded
- Equity LTCG threshold: 365 days
- Gold/Unlisted/Real Estate LTCG threshold: 730 days
- Equity LTCG rate: 12.5% (post-Budget 2024)
- Equity STCG rate: 20%
- Debt MF: always taxed at income slab rate
- Real estate pre-July 23 2024 LTCG: choice of 12.5% flat OR 20% with CII indexation
- Section 112A exemption: ₹1,25,000 per FY on listed equity LTCG

## CII Table
Hardcoded from FY 2001-02 (index 100) through FY 2026-27 (index 390).
