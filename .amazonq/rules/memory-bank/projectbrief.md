# TaxSetu — Project Brief

## Purpose
TaxSetu is a single-file Progressive Web App (PWA) for Indian investors to audit realized capital gains and plan tax liability across financial years.

## Core Goals
- Log investment sale transactions (buy/sell details)
- Auto-classify gains as STCG or LTCG per Indian tax law
- Calculate tax liability using correct rates per asset type
- Apply CII-based indexation comparison for eligible real estate
- Track Section 112A equity LTCG exemption (₹1.25 Lakh/FY)
- Surface tax-loss/profit harvesting opportunities
- Work offline as an installable PWA on mobile

## Target Users
Indian retail investors filing ITR with capital gains from equities, mutual funds, gold, unlisted shares, and real estate.

## Constraints
- Single HTML file — no build tools, no backend, no external APIs
- All data stored in browser `localStorage`
- Must work on mobile browsers (iOS Safari, Android Chrome)
