# TaxSetu — System Patterns

## Architecture
Single HTML file containing all HTML, CSS, and JavaScript. React 18 is loaded from CDN and rendered via Babel standalone (JSX transpiled in-browser). No bundler, no npm.

## File Structure
```
taxsetu_pwa_master_file.html
  ├── <head>         — meta, Tailwind CDN, fonts, Lucide icons, PWA manifest injection, SW registration
  ├── <body>         — #root div, React/ReactDOM/Babel CDN scripts
  └── <script type="text/babel">
        ├── CII_TABLE          — constant: Cost Inflation Index 2001-02 to 2026-27
        ├── getFinancialYear() — utility: date → "YYYY-YY" FY string
        ├── INITIAL_TRANSACTIONS — seed data (3 sample transactions)
        └── App()              — single root React component
              ├── State
              ├── processedTransactions (useMemo) — core tax computation
              ├── stats (useMemo)                 — FY-level aggregates
              └── JSX render
                    ├── Header
                    ├── Audit Summary Board
                    ├── Tab Controls (Audit Ledger | Indexation | Tax Planner)
                    ├── Tab Views (conditional render)
                    ├── Footer FAB
                    ├── Add Transaction Modal
                    └── PWA Install Guide Drawer
```

## State Management
All state lives in the single `App` component via `useState`:
- `transactions` — array of raw transaction objects, persisted to `localStorage`
- `activeTab` — `'dashboard' | 'indexation' | 'harvesting'`
- `selectedFY` — string e.g. `'2025-26'`
- `taxSlab` — number (10 | 15 | 20 | 30 | 39), user's income tax bracket
- `showAddModal`, `showInstallGuide`, `isInstalledStandalone` — UI booleans
- `formData` — controlled form state for the add-transaction modal

## Core Computation: processedTransactions
Runs on every `transactions` or `taxSlab` change. Per transaction:
1. Compute `holdingDays`, `buyCost`, `saleValue`, `absoluteGain`
2. Classify STCG/LTCG and assign `taxRate` by `assetType`:
   - `equity`: LTCG ≥365 days @ 12.5%, STCG @ 20%
   - `debt_mf`: always STCG @ user's `taxSlab`
   - `gold_unlisted`: LTCG ≥730 days @ 12.5%, STCG @ `taxSlab`
   - `real_estate`: LTCG ≥730 days @ 12.5%, STCG @ `taxSlab`
3. For real estate LTCG where `buyDate < 2024-07-23`: set `holdsIndexedOption = true`, compute `indexedCost` using CII ratio, compare `tax@12.5%` vs `tax@20%+indexation`, pick lower.

## Core Computation: stats
Aggregates for the selected FY:
- `totalEquityLTCG`, `totalOtherLTCG`, `totalSTCG`, `netGain`
- `adjustedTax` — total tax minus Section 112A exemption benefit (first ₹1.25L of equity LTCG is tax-free)
- `equityTaxExemptGains` = `min(totalEquityLTCG, 125000)`

## PWA Implementation
- Manifest injected dynamically as a Blob URL (avoids needing a separate manifest.json file)
- Service Worker registered from a Blob URL (cache-first strategy for `/` and `/index.html`)
- SW registration skipped in sandboxed environments (usercontent.goog, webcontainer.io, blob: protocol)
- Standalone detection via `matchMedia('(display-mode: standalone)')` and `navigator.standalone`

## Styling Conventions
- Tailwind CSS (CDN) utility classes throughout
- Font: Plus Jakarta Sans (Google Fonts)
- Icons: Lucide (CDN), rendered via `lucide.createIcons()` called after 500ms timeout
- Color palette: slate (neutrals), emerald (primary/positive), rose (negative/loss), amber (warnings/indexed)
- Mobile-safe padding: `pb-safe` on body, `pb-28` on main content to clear fixed footer
