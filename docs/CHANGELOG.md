# Changelog

All notable changes to TaxSetu will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

---

## [1.0.0] — 2025-11-05

### Added
- **Transaction CRUD**: Add investment sale transactions via slide-up modal, delete via trash icon
- **4 Asset Types**: equity, debt_mf, gold_unlisted, real_estate with distinct tax treatment
- **Auto Classification**: STCG/LTCG determined by holding period per asset type
- **Tax Computation**: Correct tax rates applied per asset type and classification
- **CII Indexation**: Cost Inflation Index table from FY 2001-02 to FY 2026-27
- **Real Estate Dual Option**: Side-by-side comparison of 12.5% flat vs 20% indexed tax
- **Section 112A Exemption**: ₹1.25L equity LTCG exemption tracking with progress bar
- **Tax Harvesting Insights**: Remaining headroom display and harvesting suggestions
- **FY Filtering**: Financial year selector in header, all views filter by selected FY
- **Income Slab Selector**: User income tax bracket dropdown (10%, 15%, 20%, 30%, 39%)
- **3-Tab UI**: Audit Ledger (transaction cards), Indexation (CII comparison), Tax Planner (exemptions)
- **Audit Summary Board**: 4-card grid showing LTCG, STCG, total tax, and net gain for the FY
- **PWA Support**: Dynamic manifest injection via Blob URL
- **Service Worker**: Cache-first offline strategy via Blob URL registration
- **Sandbox Detection**: SW registration skipped in Google previews, StackBlitz, and blob: protocol
- **Standalone Detection**: Detects iOS/Android PWA mode to show/hide install guide
- **PWA Install Guide**: Auto-shown slide-up drawer with add-to-homescreen instructions
- **LocalStorage Persistence**: Transactions saved across sessions
- **Seed Data**: 3 sample transactions (equity, unlisted, real estate) for new users
- **Mobile-First UI**: Tailwind CSS, Plus Jakarta Sans, safe area padding, portrait layout
- **Lucide Icons**: Icons rendered via CDN with post-render initialization

### Technical
- Single HTML file architecture (~884 lines)
- React 18 + Babel Standalone + Tailwind CSS (all CDN)
- No build tools, no npm, no backend

[1.0.0]: https://github.com/gurukiranbuilder/TaxSetu/releases/tag/v1.0.0