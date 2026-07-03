# TaxSetu — Progress

## Completed
- [x] Single-file PWA architecture
- [x] PWA manifest (dynamic Blob injection)
- [x] Service Worker with cache-first strategy (sandbox-safe)
- [x] React 18 + Babel standalone setup
- [x] Transaction data model and localStorage persistence
- [x] Financial year utility (`getFinancialYear`)
- [x] CII table (2001-02 to 2026-27)
- [x] Tax computation engine (`processedTransactions` memo)
  - [x] Equity STCG/LTCG (365-day threshold)
  - [x] Debt MF (slab rate)
  - [x] Gold & Unlisted (730-day threshold)
  - [x] Real Estate (730-day threshold + indexation option)
- [x] FY-level stats aggregation with Section 112A exemption
- [x] Audit Ledger tab (filtered by FY, per-transaction cards)
- [x] Indexation tab (CII comparison table for eligible real estate)
- [x] Tax Planner tab (harvesting headroom, exemption progress bar)
- [x] Add Transaction slide-up modal
- [x] PWA Install Guide drawer
- [x] Mobile-first responsive UI (Tailwind + Plus Jakarta Sans)
- [x] Seed data (3 sample transactions across asset types)

## Not Started
- [ ] Edit existing transaction
- [ ] CSV/Excel import & export
- [ ] Grandfathering (Jan 31, 2018 FMV) for pre-2018 equity LTCG
- [ ] Capital loss carry-forward and set-off
- [ ] Surcharge + cess on computed tax
- [ ] Multi-year summary / tax trend chart
