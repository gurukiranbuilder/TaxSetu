# UI Flow

## Overview

TaxSetu has a mobile-first, single-screen layout with tab-based navigation. The entire UI is rendered by the `App()` React component.

## Layout Structure

```
┌──────────────────────────────┐
│         Header               │  ← Sticky top bar
│  TaxSetu  [2025-26 ▼] [30%] │     (title, FY badge, slab selector)
├──────────────────────────────┤
│     Audit Summary Board      │  ← 4 stat cards in a grid
│  ┌────┐ ┌────┐ ┌────┐ ┌────┐│
│  │LTCG│ │STCG│ │Tax │ │NET ││
│  └────┘ └────┘ └────┘ └────┘│
├──────────────────────────────┤
│   Tab Controls               │  ← 3 tabs
│ [Audit Ledger] [Indexation]  │
│ [Tax Planner]                │
├──────────────────────────────┤
│                              │
│     Tab Content              │  ← Conditionally rendered
│                              │
│                              │
├──────────────────────────────┤
│                    [FAB +]   │  ← Fixed bottom-right button
└──────────────────────────────┘
```

## Tabs

### 1. Audit Ledger (`activeTab = 'dashboard'`)

Shows a filtered list of transactions for the selected financial year.

- **Empty state**: "No transactions for FY 2025-26" with a prompt to add one via the FAB
- **Card layout**: Each transaction renders as a card showing:
  - Asset name, type badge (equity, debt_mf, gold_unlisted, real_estate)
  - Buy/Sell dates and holding period
  - Buy cost, sale value, absolute gain (green for profit, red for loss)
  - Classification badge (STCG/LTCG) with tax rate
  - Calculated tax amount
  - Delete button (trash icon) — removes transaction with confirmation
- **Scrollable**: Cards list is scrollable within the main content area

### 2. Indexation (`activeTab = 'indexation'`)

Shows a comparison table for real estate LTCG transactions eligible for indexation.

- **Eligibility filter**: Only shows transactions where `assetType === 'real_estate'`, `classification === 'LTCG'`, and `holdsIndexedOption === true`
- **Empty state**: "No real estate LTCG transactions eligible for indexation comparison in FY 2025-26"
- **Table columns per eligible transaction**:
  - Asset name
  - Buy cost / Indexed cost
  - Gain (absolute) / Gain (indexed)
  - Tax at 12.5% flat / Tax at 20% indexed
  - Recommended option (highlighted in green)
  - Savings amount

### 3. Tax Planner (`activeTab = 'harvesting'`)

Shows Section 112A exemption tracking and tax harvesting opportunities.

- **Exemption Progress Bar**: Visual bar showing how much of the ₹1.25L equity LTCG exemption has been used
- **Headroom Display**: "You can still realize ₹X,XXX in equity LTCG tax-free this FY"
- **Harvesting Suggestions**: If remaining headroom > 0, shows a prompt about booking profits within the exempt limit

## Modals & Overlays

### Add Transaction Modal (`showAddModal === true`)

- **Trigger**: FAB (+) button in bottom-right corner
- **Animation**: Slides up from bottom (`slideUp` CSS animation, 0.25s cubic-bezier)
- **Overlay**: Semi-transparent backdrop, tapping it dismisses the modal
- **Form fields**:
  - Asset Name (text input)
  - Asset Type (select: equity, debt_mf, gold_unlisted, real_estate)
  - Buy Date (date input)
  - Buy Price (number input, price per unit)
  - Quantity (number input)
  - Sell Date (date input)
  - Sell Price (number input, price per unit)
- **Actions**: "Add Transaction" button (submits) | "Cancel" (dismisses)
- **Validation**: All fields required; buyPrice, qty, sellPrice must be positive numbers

### PWA Install Guide Drawer (`showInstallGuide === true`)

- **Trigger**: Automatically shown on first visit if not installed as standalone PWA (controlled by `isInstalledStandalone` state)
- **Animation**: Slides up from bottom (same as add modal)
- **Content**: Step-by-step instructions:
  1. Tap the Share/Action button in your browser
  2. Scroll down and tap "Add to Home Screen"
  3. Tap "Add" in the top-right corner
- **Dismiss**: Tap anywhere outside or the close button
- **Persistence**: Shown once per session; dismissed permanently on close

## Footer FAB

- **Position**: Fixed bottom-right (`fixed bottom-6 right-6`)
- **Icon**: Plus (+) from Lucide
- **Color**: Emerald green (`bg-emerald-600`)
- **Shadow**: Large drop shadow for visibility
- **Action**: Sets `showAddModal = true`

## State-Driven UI

| State | UI Behavior |
|-------|------------|
| No transactions exist | Empty state message + FAB visible |
| No transactions for selected FY | "No transactions" message for each tab |
| Real estate with indexation option | Indexation tab shows comparison table |
| No eligible real estate | Indexation tab shows empty state |
| Standalone PWA mode | Install guide not shown |
| First visit (non-standalone) | Install guide auto-shown |
| Add modal open | Background content dimmed, modal slides up |

## Responsive Behavior

- **Mobile** (default): Single column, max-width `max-w-md`, centered
- **Desktop**: Same centered card layout, wider horizontal padding
- **Portrait only**: App is designed for portrait orientation
- **Safe areas**: `pb-safe` class on body for iPhone notch/home indicator
- **Bottom clearance**: `pb-28` on main content to prevent FAB overlap