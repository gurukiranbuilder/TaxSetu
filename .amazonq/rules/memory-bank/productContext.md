# TaxSetu — Product Context

## Problem Being Solved
Indian investors struggle to manually compute capital gains tax across multiple asset classes with different holding period thresholds, tax rates, and special provisions (indexation, Section 112A exemption). Existing tools are either desktop-only, require login, or don't handle the post-Budget 2024 dual-rate real estate option.

## How It Should Work
1. User adds a realized sale entry (asset name, type, buy/sell dates, prices, quantity).
2. App auto-computes holding period, gain classification, applicable tax rate, and estimated tax.
3. Dashboard shows FY-filtered audit ledger with per-transaction breakdown.
4. Indexation tab shows side-by-side 12.5% flat vs 20% indexed comparison for eligible real estate.
5. Tax Planner tab shows Section 112A exemption utilization and remaining harvesting headroom.

## Key UX Decisions
- Mobile-first, portrait-only layout (max-w-md centered)
- Slide-up modal for adding transactions (native app feel)
- Sticky header with FY badge; fixed bottom FAB for quick entry
- No login, no cloud — privacy-first local storage
- PWA installable via "Add to Home Screen" with guided drawer
