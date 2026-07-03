# TaxSetu — Active Context

## Current State
The project is a complete, working single-file PWA. All three tabs (Audit Ledger, Indexation, Tax Planner) are functional with sample data seeded.

## What Works
- Full CRUD for transactions (add via modal, delete via trash icon)
- Tax computation for all 4 asset types
- CII indexation comparison for pre-July 2024 real estate LTCG
- Section 112A exemption tracking and harvesting opportunity display
- FY filtering and income slab selector
- PWA install guide drawer
- localStorage persistence

## Known Limitations / Potential Next Work
- No edit functionality for existing transactions (delete + re-add only)
- No CSV/Excel import or export
- Lucide icons rely on a 500ms `setTimeout` hack to initialize after React renders — fragile
- No surcharge/cess calculation on top of base tax rates
- No support for grandfathering (FMV as of Jan 31, 2018 for pre-2018 equity LTCG)
- No carry-forward loss set-off logic
- `taxSlab: 39` label says "39% Surcharge" but is treated as a flat rate, not a surcharge on base tax

## Active File
`taxsetu_pwa_master_file.html` — the entire application lives here.
