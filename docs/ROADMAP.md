# Roadmap

## Current State (v1.0.0)

The app is feature-complete for basic capital gains auditing. All three tabs (Audit Ledger, Indexation, Tax Planner) are functional with sample data seeded.

## Known Limitations

These are gaps in the current implementation that users may encounter:

| Limitation | Impact | Priority |
|-----------|--------|----------|
| No edit for existing transactions | Users must delete + re-add to correct an entry | High |
| No CSV/Excel import or export | Manual data entry for bulk transactions | High |
| No surcharge/cess on base tax | Tax liability may be understated | Medium |
| No grandfathering (FMV Jan 31, 2018) | Pre-2018 equity LTCG not handled correctly | Medium |
| No capital loss carry-forward/set-off | Cannot offset gains with prior losses | Medium |
| `taxSlab: 39` is treated as flat rate | Should be a surcharge on base tax, not a flat rate | Low |
| Lucide icons rely on 500ms setTimeout | Fragile initialization — may miss icons on fast renders | Low |

## Planned Features

These are sourced from the existing `product-brainstorm.md` and the memory bank notes.

### Near-term (Next Priority)

- [ ] **Edit Transaction** — Allow editing existing transactions inline or via prefilled modal
- [ ] **CSV Export** — Export transaction ledger and tax summary to CSV file
- [ ] **CSV Import** — Bulk import transactions from a CSV template
- [ ] **Grandfathering Support** — Compute FMV as of January 31, 2018 for equity shares acquired before 2018

### Medium-term

- [ ] **Surcharge & Cess Calculation** — Apply income tax surcharge slabs and 4% health & education cess
- [ ] **Capital Loss Carry-Forward** — Track losses across FYs, apply set-off rules (Section 74)
- [ ] **Multi-Year Summary** — Aggregate view across all financial years with trend visualization
- [ ] **Tax Slab Surcharge Fix** — Properly implement surcharge as percentage on base tax, not a flat rate

### Long-term / Exploratory

- [ ] **Slack Notification Integration** — Alerts when anomaly metrics spike (from product-brainstorm)
- [ ] **Dashboard CSV Export** — Admin offline data for weekly presentations (from product-brainstorm)
- [ ] **User Authentication** — AWS Cognito integration for multi-device sync (from product-brainstorm)
- [ ] **Cloud Sync** — Optional encrypted cloud backup of transaction data
- [ ] **ITR Pre-fill** — Generate pre-filled Schedule CG (Capital Gains) for ITR filing

## Non-Goals

These are explicitly out of scope for the project's current direction:
- Multi-currency / non-Indian tax support
- Real-time market data or portfolio tracking
- Tax filing / e-filing integration
- Mobile native app (iOS/Android) — PWA covers this
- Backend server or database