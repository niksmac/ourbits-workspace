---
title: Reposition ourbits.io around product passports, plus unlisted battery landing page
description: Stage 1 removes unprovable claims and refocuses the site on product passports with design-partner, standards, trust and about pages. Stage 2 builds an unlisted /battery-passport page for 25 outreach messages, kept out of the nav until 3+ pilots say yes.
repo: ourbits_io_website
status: wip
createdDate: 2026-11-24
modifiedDate: 2026-11-24
branch: site-reposition (worktree ourbits_io_website/.claude/worktrees/site-reposition, from develop)
---

# Plan

Work happens in a worktree on branch `site-reposition`, cut from `develop`. Nothing is merged; the user reviews and merges.

## Stage 1: right whatever the validation says

Remove from the homepage and site:

- [x] KPI block in `CircularNetworkMap` (128,420 products, $4.8M recovered, etc.)
- [x] `ImpactSection` (NOAA CO₂ chart, "climate cost") and the 27 billion devices stat in `AboutSection`
- [x] `PowersExperience` tabs (incl. Recycle Credit Certification, Rewards)
- [x] `CoreValues` (incl. "first platform" claim)
- [x] "blockchain-backed proof" in the mission, "Join the mission", "Be a part of the Sustainable Future", "Act for the Sustainable Future"
- [x] `HoldMyBillSection` from the homepage (footer link stays)
- [x] meta keywords (root layout and `buildMetadata`)

Change:

- [x] Hero H1, subhead, CTAs (primary "Become a design partner" → /design-partners, secondary "See how it works" → /#how-it-works)
- [x] Homepage "How it works": 4 steps; full 12-step map moves to /vision labelled as roadmap
- [x] Ledger wording → "tamper-evident audit trail"
- [x] Nav: Product, How it works, Standards, About, Insights + "Become a design partner" button
- [x] Meta title "Ourbits | Product passports that keep working after the sale."

Add:

- [x] /design-partners (who it's for, free 60-day pilot, what we ask in return, application form)
- [x] /standards (UNTP alignment; Annex XIII and DIN DKE SPEC 99100 mapping status, done vs in progress)
- [x] /trust (hosting region, DPA, subprocessors, consent rules)
- [x] /about (team, HoldMyBill on iOS and Android). Lightrains / "since 2009" dropped at the user's request: no connection to Lightrains anywhere on the site
- [x] /vision (12-step roadmap map)
- [x] Homepage Insights section with the 3 latest posts (moved up from the footer)

## Stage 2: build now, keep out of the nav

- [x] /battery-passport: noindex, not in sitemap or nav. H1, deadline block (18 Feb 2027, who, what), after-sale angle, CTA "Book a passport readiness check"
- [x] Two Insights posts for e-bike importers
- [x] Tracking: `?ref=` codes on outreach links, events for visit / CTA / booking, ref carried into the booking submission; reply tracker sheet

Promotion rule (not built, documented): if 3+ pilots say yes, the battery H1 becomes the homepage hero, /battery-passport goes into the nav, and the generic thesis drops to a subhead.

## Facts used (verified)

- Regulation (EU) 2023/1542 Art. 77(1): from 18 February 2027 each LMT battery, each industrial battery > 2 kWh and each EV battery placed on the market or put into service needs a battery passport. No capacity threshold for LMT.
- Art. 77(4): the economic operator placing the battery on the market keeps the passport accurate, complete and up to date; may authorise another operator in writing.
- Art. 77(3): accessible through the QR code (Art. 13(6)) linking to a unique identifier.
- Art. 77(7): repurposing/remanufacturing → new passport linked to the original; waste status → responsibility moves to producer / PRO / waste operator. Art. 77(8): passport ceases after recycling.
- Annex XIII: four access tiers (public model data; legitimate-interest model data; authorities-only test reports; per-battery data: performance/durability values, state of health, status original/repurposed/re-used/remanufactured/waste, usage data and negative events).

## Decisions

- Every product, standards and trust claim carries a status (Live / Done / With design partners / In progress / Planned) via `StatusBadge`; data lives in `src/lib/standards.js` and `src/lib/trust.js` with code evidence in comments.
- The audit trail is described as in development: `verificationLedger` is null in the API, so nothing is signed or chained today.
- Mission text moved to /about without "blockchain-backed proof"; the homepage no longer has a Mission and Vision block.
- Blog URLs stay at `/blog`; the label is "Insights" everywhere.
- Batteriepasswerk (Hamburg battery passport SaaS: free pilot, EU hosting in Germany, DIN DKE SPEC 99100) was used as the benchmark for what /standards and /trust must answer. It is not named on the site.
- Lead forms reuse `POST api.holdmybill.com/v1/public/contact` (category `ourbits_partnership`, emails hello@ourbits.io). Company, product and outreach ref go into the message text because the API drops unknown fields.
- Visits: optional Plausible (cookieless), enabled by `NEXT_PUBLIC_PLAUSIBLE_DOMAIN`. Tracker: [001_doc_battery-outreach-tracker](../docs/001_doc_battery-outreach-tracker.md).

## Open (needs the user)

- Confirm MongoDB Atlas, Redis and EC2 regions, then update `src/lib/trust.js` (hosting row to `done`, subprocessor locations).
- Draft the DPA; /trust says it is in preparation.
- Add team names, roles and bios to `TEAM` in `src/lib/company.js`.
- Review the pilot "what we ask in return" list on /design-partners.
- Confirm production `ALLOWED_ORIGINS` in ourbits_hmb_api includes https://ourbits.io, or the lead forms fail with a CORS error (API repo; not changed here).
- Set `NEXT_PUBLIC_PLAUSIBLE_DOMAIN` and create the Plausible goals.
- Follow-up in ourbits_hmb_web: `app/about/page.js` still makes ledger/blockchain claims; the privacy policy names no subprocessors and lists AWS/Google Cloud/Azure for document processing, which doesn't match the code (OpenRouter → Gemini).

## Log

- 2026-11-24: worktree created; plan written.
- 2026-11-24: `eef7968` stage 1; `3b04f06` stage 2. `npm run build` passes (31 routes); eslint 0 errors (3 existing `<img>` warnings). HTTP smoke test on `next start`: all new routes 200, title correct, no keywords meta, no removed metrics/claims, /battery-passport noindex and absent from the sitemap. Forms not submitted (they hit the live API).
- 2026-11-24: `5c6844f` removed all Lightrains references from /about (user: any connection to Lightrains is banned).
- 2026-11-24: `134719d` humanizer pass. Fast-forwarded `develop` to `134719d` (local only, not pushed); `npm run build` on develop passes.
