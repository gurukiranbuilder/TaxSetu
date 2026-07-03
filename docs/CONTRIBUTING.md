# Contributing

## Project Overview

TaxSetu is a **single-file PWA** — `index.html` contains all HTML, CSS, and JavaScript. There is no build pipeline, no package manager, and no backend.

## Development Workflow

### Local Setup

1. **Clone the repository:**
   ```bash
   git clone https://github.com/gurukiranbuilder/TaxSetu.git
   cd TaxSetu
   ```

2. **Open the app:**
   ```bash
   open index.html
   ```
   Or simply double-click the file in your file manager. No server required for basic development.

### Making Changes

All code lives in `index.html`. The file is organized as:

| Lines | Section |
|-------|---------|
| 1–125 | `<head>` — meta tags, CDN scripts, PWA setup, custom CSS |
| 126–130 | `<body>` — root `<div>` |
| 131–135 | CDN scripts (React, ReactDOM, Babel) |
| 137–884 | `<script type="text/babel">` — application logic |

The Babel script block contains:

```
Constants (CII_TABLE, getFinancialYear, INITIAL_TRANSACTIONS)
  → App() component
    → useState hooks
    → useMemo (processedTransactions)
    → useMemo (stats)
    → JSX render
```

### Best Practices

1. **Preserve the single-file structure** — Don't extract code into separate files unless there's a strong reason. The single-file approach eliminates build tooling complexity.
2. **Keep CDN dependencies minimal** — All external libraries are loaded from CDN. Adding new CDN scripts should be justified.
3. **Use Tailwind utility classes** — Avoid custom CSS where possible. If you need custom styles, add them to the `<style>` block in `<head>`.
4. **Test on mobile** — The app is mobile-first. Always test on actual mobile devices or Chrome DevTools mobile emulation.
5. **Maintain PWA functionality** — Ensure service worker registration and manifest injection still work after changes. Test offline mode.

### Code Style

- **No TypeScript** — Babel Standalone does not support TS. Stick to plain JavaScript.
- **ES6+ features** — Use `const`, `let`, arrow functions, destructuring, template literals, etc. Babel Standalone handles transpilation.
- **JSX conventions** — Use fragments (`<>...</>`) for grouping, conditional rendering with ternary operators or `&&`.
- **Naming**: camelCase for variables/functions, PascalCase for components.

### Testing

There is no automated test framework. Manual testing covers:

- **CRUD**: Add a transaction, verify it appears in the ledger, delete it, verify removal
- **Tax computation**: Add known test cases and verify computed tax matches expected value
- **FY filtering**: Verify transactions appear only in the correct financial year
- **Indexation tab**: Add a real estate transaction with pre-July 2024 buy date, verify dual-option display
- **PWA**: Install the app via "Add to Home Screen", verify it opens in standalone mode, test offline
- **localStorage**: Verify data persists across page refreshes, verify seed data appears on first visit

### Debugging

- **React DevTools**: Helpful for inspecting component state and props
- **console.log**: The file already uses `console.log` and `console.warn` for service worker registration status
- **Lucide icons**: If icons don't render, inspect the 500ms `setTimeout` — this is a known fragile point

## Keeping Docs in Sync (AI-Assisted Workflow)

You don't need to remember which docs to update after a change. Simply tell any AI assistant:

> **"Update docs to reflect the latest changes."**

The AI will read the current code, check all docs against it, and update every file that's out of sync. This works across any AI tool (Claude, ChatGPT, GitHub Copilot, etc.).

### How It Works

The AI uses this mapping to figure out which docs to update based on what changed in the code:

| If the code change touches… | The AI updates… |
|----------------------------|-----------------|
| New feature, new user segment, or changed product scope | `docs/PRODUCT_OVERVIEW.md` |
| New state variable, component, or file structure | `docs/ARCHITECTURE.md` |
| New transaction field, asset type, or persistence | `docs/DATA_MODEL.md` |
| New tax rule, rate, exemption, or computation | `docs/TAX_RULES.md` |
| New UI screen, tab, modal, or navigation | `docs/UI_FLOW.md` |
| Manifest, service worker, or offline behavior | `docs/PWA_SETUP.md` |
| Anything at all | `docs/CHANGELOG.md` + `docs/ROADMAP.md` |

If no relevant changes exist for a doc, it's left untouched — no unnecessary updates.

### The Full Feature Pipeline

```
product-brainstorm.md        ← Free-form ideation (dump ideas, no rules)
        ↓
docs/ROADMAP.md              ← Move polished ideas here as checklist items
        ↓
index.html ← Implement the code
        ↓
Ask AI: "Update docs"       ← Auto-syncs all affected docs + CHANGELOG + ROADMAP
```

1. **Ideate freely** in `product-brainstorm.md` — messy notes, pros/cons, open questions. No structure required.
2. **Commit to build**: Move a summary of the idea into `docs/ROADMAP.md` as a checklist item. Optionally mark the brainstorm entry as `[MOVED TO ROADMAP]`.
3. **Implement** the code in `index.html`.
4. **Update docs**: Ask any AI to "update docs to reflect the latest changes" — it will:
   - Read the code changes
   - Update the relevant docs from the mapping table above
   - Add a `CHANGELOG.md` entry
   - Mark the item as `[x]` in `ROADMAP.md`

## Pull Request Process

1. Ensure your changes are tested manually (see Testing section above)
2. Run "update docs" step to sync all documentation
3. Open a PR with a clear title and description of what was changed and why
