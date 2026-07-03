# Product Overview

## Elevator Pitch

**TaxSetu** is a privacy-first, offline-capable Progressive Web App that helps Indian retail investors audit their realized capital gains and plan tax liability — without logging in, installing software, or uploading data to any server.

---

## Problem Statement

Indian investors face a complex tax landscape when filing capital gains:

- **Multiple asset types** (equity, debt mutual funds, gold, unlisted shares, real estate) each have different holding period thresholds for long-term vs short-term classification
- **Varying tax rates** — 12.5% flat, 20% flat, or income slab rates depending on asset type and holding period
- **Indexation math** — Real estate gains can be taxed at 12.5% flat OR 20% with CII indexation; the optimal choice depends on inflation during the holding period
- **Section 112A exemption** — The first ₹1.25 Lakh of equity LTCG per financial year is tax-free, but tracking utilization across multiple transactions is tedious
- **Tax harvesting** — Knowing how much headroom remains for tax-free gains requires real-time computation

Existing solutions are either desktop-only, require account creation and cloud storage, or don't handle the post-Budget 2024 dual-rate real estate option.

---

## Target Users

| User | Need |
|------|------|
| **Indian retail investor** | Track capital gains across multiple asset classes for ITR filing |
| **Salaried employee with investments** | Compute tax liability on equity, MF, and real estate sales |
| **Chartered Accountant / tax filer** | Quick audit of a client's realized gains without setup overhead |
| **NRI with Indian investments** | Understand capital gains tax on Indian asset sales |

---

## Core Features

### 1. Transaction Ledger (Audit Tab)
- Add investment sale transactions with buy/sell dates, prices, and quantity
- Auto-computes holding period, gain classification (STCG/LTCG), and tax for each transaction
- Filter by financial year to see only relevant entries
- Color-coded cards: green for profit, red for loss

### 2. Indexation Comparison (Indexation Tab)
- For real estate LTCG with pre-July 2024 purchase, shows a side-by-side comparison:
  - **Option A**: 12.5% flat tax on absolute gain
  - **Option B**: 20% tax on CII-indexed gain
- Automatically recommends the lower-tax option and shows the savings

### 3. Tax Planner (Harvesting Tab)
- Visual progress bar showing Section 112A exemption utilization (₹1.25L/FY)
- Displays remaining headroom for tax-free equity LTCG
- Suggests profit-booking opportunities within the exempt limit

### 4. PWA & Offline
- Installable on mobile home screen (Android & iOS)
- Works fully offline after first visit
- All data stored locally in the browser — no cloud, no login

---

## How It Works

```
User adds a transaction
        ↓
App computes holding period (days between buy and sell)
        ↓
Classifies gain as STCG or LTCG based on asset type + holding days
        ↓
Applies correct tax rate per asset type and classification
        ↓
For eligible real estate: compares 12.5% flat vs 20% indexed, picks lower
        ↓
Aggregates all transactions for the selected financial year
        ↓
Applies Section 112A exemption (first ₹1.25L equity LTCG tax-free)
        ↓
Displays results in 3 tabs: Audit Ledger | Indexation | Tax Planner
```

All computation happens in the browser. No data is sent anywhere.

---

## Key Differentiators

| Aspect | TaxSetu | Typical Alternatives |
|--------|---------|---------------------|
| **Setup** | Open one HTML file | Install software, create account |
| **Privacy** | 100% local (localStorage) | Data stored on cloud servers |
| **Offline** | Fully functional offline | Requires internet connection |
| **Cost** | Free | Often paid or freemium |
| **Post-Budget 2024** | Handles dual-rate real estate option | Many tools not updated |
| **Mobile** | Installable PWA, native-like experience | Desktop-only or requires app store |

---

## Current Limitations

See [ROADMAP.md](./ROADMAP.md) for the full list, but key gaps include:
- No edit for existing transactions (delete + re-add only)
- No CSV import/export for bulk data
- No grandfathering (FMV as of Jan 31, 2018) for pre-2018 equity
- No surcharge or health & education cess on computed tax
- No capital loss carry-forward or set-off

---

## Evolution

| Version | Date | Summary |
|---------|------|---------|
| v1.0.0 | 2025-11-05 | Initial release — full CRUD, 4 asset types, indexation, 112A exemption, PWA |

See [CHANGELOG.md](./CHANGELOG.md) for detailed release notes.

---

## Quick Links

| Document | What It Covers |
|----------|---------------|
| [ARCHITECTURE.md](./ARCHITECTURE.md) | File structure, component tree, state management, rendering pipeline |
| [DATA_MODEL.md](./DATA_MODEL.md) | Transaction schema, computed fields, CII table, persistence |
| [TAX_RULES.md](./TAX_RULES.md) | Full encoded tax logic — rates, thresholds, indexation, exemptions |
| [UI_FLOW.md](./UI_FLOW.md) | All screens, tabs, modals, navigation flow |
| [PWA_SETUP.md](./PWA_SETUP.md) | Manifest injection, Service Worker, offline behavior |
| [ROADMAP.md](./ROADMAP.md) | Known limitations, planned features, non-goals |
| [CHANGELOG.md](./CHANGELOG.md) | Version history and release notes |
| [CONTRIBUTING.md](./CONTRIBUTING.md) | Development workflow, testing, docs sync process |