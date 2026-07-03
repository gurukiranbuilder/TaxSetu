# Architecture

## Overview

TaxSetu is a **single-file Progressive Web App**. All HTML, CSS, and JavaScript live in `index.html`. There is no build pipeline, no package manager, and no backend server.

## Technology Stack

| Layer | Technology | Source |
|-------|-----------|--------|
| UI Framework | React 18 | `unpkg.com/react@18/umd/react.production.min.js` |
| JSX Transpiler | Babel Standalone | `unpkg.com/@babel/standalone/babel.min.js` |
| Styling | Tailwind CSS | `cdn.tailwindcss.com` |
| Icons | Lucide | `unpkg.com/lucide@latest` |
| Typography | Plus Jakarta Sans | Google Fonts |
| Storage | `localStorage` | Browser API |
| Offline | Service Worker + Cache API | Browser API |

## File Structure

```
index.html
  ├── <head>
  │     ├── Meta tags (charset, viewport, PWA Apple meta)
  │     ├── Tailwind CSS CDN
  │     ├── Google Fonts (Plus Jakarta Sans)
  │     ├── Lucide Icons CDN
  │     ├── PWA manifest injection (dynamic Blob URL script)
  │     ├── Service Worker registration (Blob URL script)
  │     └── <style> — custom CSS (slide-up animation, body defaults)
  ├── <body>
  │     └── <div id="root"> — React mount point
  └── <script type="text/babel"> — Application source
        ├── Constants
        │     ├── CII_TABLE — Cost Inflation Index (2001-02 to 2026-27)
        │     ├── getFinancialYear() — date → "YYYY-YY" helper
        │     └── INITIAL_TRANSACTIONS — 3 seed transactions
        └── App() — Single React root component
              ├── State (useState)
              ├── processedTransactions (useMemo) — Tax computation engine
              ├── stats (useMemo) — FY-level aggregations
              └── JSX Render
                    ├── Header (title, FY badge, tax slab selector)
                    ├── Audit Summary Board (cards showing totals)
                    ├── Tab Controls (Audit Ledger | Indexation | Tax Planner)
                    ├── Tab Views (conditional on activeTab)
                    │     ├── Audit Ledger — per-transaction card list
                    │     ├── Indexation — CII comparison table
                    │     └── Tax Planner — 112A exemption, harvesting headroom
                    ├── FAB button (fixed bottom-right, opens add modal)
                    ├── Add Transaction Modal (slide-up overlay)
                    └── PWA Install Guide Drawer (slide-up overlay)
```

## Rendering Pipeline

```
User Action (add/delete transaction)
  → setTransactions() updates state
    → React re-renders App()
      → processedTransactions (useMemo) recalculates all tax fields
      → stats (useMemo) recalculates FY aggregates
      → JSX renders updated UI
      → lucide.createIcons() called after 500ms setTimeout
```

## State Management

All state lives in the single `App()` component using `useState`. There is no context, no reducer, and no external state library.

| State Variable | Type | Purpose |
|---------------|------|---------|
| `transactions` | `Transaction[]` | Raw transaction array, persisted to `localStorage` |
| `activeTab` | `'dashboard' \| 'indexation' \| 'harvesting'` | Current tab selection |
| `selectedFY` | `string` | Selected financial year filter |
| `taxSlab` | `number` | User's income tax bracket (10, 15, 20, 30, 39) |
| `showAddModal` | `boolean` | Add transaction modal visibility |
| `showInstallGuide` | `boolean` | PWA install guide drawer visibility |
| `isInstalledStandalone` | `boolean` | Whether running as standalone PWA |
| `formData` | `object` | Controlled form fields for add-transaction modal |

## Computed Data (useMemo)

**`processedTransactions`** — derived from `transactions` and `taxSlab`. Each raw transaction is extended with:
- `holdingDays`, `buyFY`, `sellFY`
- `buyCost`, `saleValue`, `absoluteGain`
- `classification` (STCG/LTCG), `taxRate`
- `holdsIndexedOption` (real estate only), `indexedCost`, `indexedGain`
- `calculatedTax`, `indexationAdvantage`, `taxOptimizationNote`

**`stats`** — derived from `processedTransactions` and `selectedFY`. Aggregates for the selected FY:
- `totalEquityLTCG`, `totalOtherLTCG`, `totalSTCG`, `netGain`
- `adjustedTax`, `equityTaxExemptGains`, `totalTax`

## Browser APIs Used

| API | Purpose |
|-----|---------|
| `localStorage` | Transaction persistence across sessions |
| `ServiceWorker` + `Cache API` | Offline support via cache-first strategy |
| `URL.createObjectURL` + `Blob` | Dynamic manifest.json and service worker injection |
| `window.matchMedia('(display-mode: standalone)')` | PWA standalone mode detection |
| `navigator.standalone` | iOS PWA standalone detection |

## Key Design Decisions

1. **Single file** — Everything in one HTML file eliminates build tooling, bundler configuration, and dependency management. Tradeoff: the file is ~884 lines and all code is global-scoped within the Babel script block.
2. **No TypeScript** — Using Babel Standalone means no TS transpilation. Types are documented in comments and this architecture doc.
3. **CDN dependencies** — React, Babel, Tailwind, and Lucide are all fetched at runtime. The app requires internet on first load but works offline afterward via the service worker cache.
4. **Blob-based PWA** — Both the manifest and service worker are generated as Blob URLs to avoid needing separate files on the server. This simplifies deployment (just host one HTML file).