---
title: Rework the holdmybill.com homepage around snap, remember, claim
description: Rebuild the homepage to the new top-to-bottom brief (hero, 3 steps, coverage, when something breaks, tools, privacy, FAQ, final CTA), fix meta and structured data, and add the save-this-claim prompt to the claim letter tool. Pricing, brand pages, rights pages, guide regrouping and Medium canonicals are tracked here as follow-ups.
repo: ourbits_hmb_web
status: wip
createdDate: 2026-11-24
modifiedDate: 2026-11-24
branch: develop (uncommitted, no worktree)
---

# Plan

All edits in `ourbits_hmb_web` on `develop`. Nothing committed until the user asks.

## In scope now

- [x] Facts check against `ourbits_hmb_app` and `ourbits_hmb_api` (statuses, extraction, reminders, export/delete, sharing, pricing, countries) so no copy promises what the app doesn't do
- [x] Hero: new H1/subhead, "Get the app" (primary, `/download`) + "Try the free claim letter" (secondary), trust line, app screen with one item card (time left + receipt attached)
- [x] How it works: 3 steps
- [x] Coverage at a glance: My Items with real statuses
- [x] When something breaks: proof ready, free claim letter, warranty vs legal guarantee, link to rights page
- [x] Free tools: claim letter first, then Repair or Replace, Extended Warranty Analyzer
- [x] Privacy: one block
- [x] Pricing teaser: held (placeholder comment in app/page.js) until pricing is final
- [x] FAQ: the 7 questions from the brief, FAQPage schema generated from the same list
- [x] Final CTA: "Snap your first bill" + store badges
- [x] Remove: FeatureGrid, PrivacySection (5-slide sticky, holds "No upsells"), BenefitsAdvantagesSection, HoldMyBillSection on home, ContactSection email form, em dash hero
- [x] Meta title/description; drop `keywords`; drop US geo tags
- [x] MobileApplication schema description matches new positioning
- [x] OG image from the new hero
- [x] Claim letter: "Save this claim and get a reminder if there's no reply in 14 days" after the letter is generated, into the app
- [x] Ourbits link: lives on /about, not the homepage
- [x] humanizer-v2 pass on all new copy
- [x] `npm run build` passes

## Follow-ups (not in this pass)

- /pricing and the homepage pricing teaser: wait for final pricing that matches the app
- /brands/[brand]: 10 brands from official warranty pages and support contacts, then 30
- /rights/[country]: launch market first, lawyer review before publishing
- Guides: regroup into Claims and refunds, Repair or replace, Warranties explained, Maintenance; delete thin posts
- /about: team section (needs names, roles, photos from the user)
- Geo tags for the launch market once chosen
- Medium: set canonical links on Medium posts to holdmybill.com (done in Medium, not in this repo)
- Real app screenshots to replace the coded hero and My Items screens

# Log

## 2026-11-24: homepage built, build passes (uncommitted on develop)

Facts checked against the app and API before writing copy. Three lines in the brief are not true today and were softened; restore them when the feature ships:

| Brief line | Why not | Shipped instead |
|---|---|---|
| "get reminded before cover runs out" (subhead, step 3, meta) | No warranty-expiry reminder is sent: `emailWarrantyExpiry` has no caller, `nextReminderAt` is never set; only maintenance reminders run | "spot cover before it runs out"; step 3: item turns orange at 30 days left (`assets_list_screen.dart` threshold) |
| "Export or delete anytime" | No data export in app or API; deletion exists (30-day retention) | "Delete anytime"; "removed within 30 days" |
| Claim letter CTA "leads into the app" with a 14-day reminder | App has no claims feature | Block with the line, 14-day calendar reminder (web, `REPLY_DAYS`), and Get the app to keep the receipt |

Other decisions:
- Hero and Coverage visuals are coded phone screens (`AppScreens.jsx`) using the app's real labels ("Your Items", Active/Expired/Needs Review, "Time Left :", "Invoice Status: Attached (1 file)"). Screen title is "Your Items", not "My Items". Replace with real screenshots when captured.
- "Rights page" links to the claim letter FAQ (`/tools/warranty-claim-letter#faq-claim-letter`) until `/rights/[country]` exists.
- All homepage copy lives in `components/landing-page/home-copy.js`; the FAQPage schema reads the same list. humanizer-v2 pass done.
- Footer "Ourbits Systems" credit now links to /about; ourbits.io only remains in Organization JSON-LD.
- MobileApplication schema already existed; description updated. FAQPage schema rebuilt from the new FAQ.
- New OG image `public/images/og-image.png` from `scripts/generate-og-image.mjs` (needs network for Google Fonts). Old og-image.jpg left in place for cached shares.
- Removed: FeatureGrid, BenefitsAdvantagesSection, AssetLifecycleSection, StickyRevealSection, ContactSection, EmailWaitlistForm; 5-slide privacy scroller replaced by one block. Hreflang alternates that pointed 10 languages at the same English URL dropped from the homepage.
- `lib/app-stores.js` shared by /download, StoreBadges, hero and claim letter.

Open (not touched, same false reminder claim): `app/llms.txt/route.js`, `app/llms-full.txt/route.js`, `app/about/page.js:74`.

## 2026-11-24: app screens rebuilt from the Flutter code

User feedback: the first coded screens didn't match the app. `AppScreens.jsx` now copies the real UI from `ourbits_hmb_app`, laid out at 390x844pt and scaled 75%:
- Hero: Your Items tab (`assets_list_screen.dart`): header + funnel button, `_AssetListItem` cards (68pt thumb, 18pt radius, Material green/orange/red, warning badge, "Time Left :"), floating nav from `main_screen.dart`. The first card's thumbnail is a bill photo, since the list shows `attachments.first`.
- Coverage: Dashboard tab (`dashboard_screen.dart`): greeting, Items Overview folder panel (Active Items / Expiring Soon / Expired), value card, Needs Attention.
- Font: Google Sans Flex (the app's `AppTheme.fontFamily`) via next/font, scoped to the phone frames.
- OG image regenerated with the same list screen.
- Remaining gaps: Material icons are approximated with the closest Lucide icons; the receipt thumbnail is drawn, not photographed. Real screenshots still preferred.

## 2026-11-24: SEO tag review

- Title now "Receipt and warranty tracker app | HoldMyBill" (user asked for the brand last, matching the `%s | HoldMyBill` template on other pages); og/twitter titles follow.
- Icons: `/icon.svg` and `/apple-touch-icon.png` were 404 on every page; layout now links app/icon0.svg, app/icon1.png, app/apple-icon.png (favicon.ico comes from the file convention).
- Sitemap lastmod for `/` and `/tools/warranty-claim-letter` bumped to 2026-11-24 (`lib/seo/last-updated.js`).
- OG image alt describes the image; FAQ heading "Frequently asked questions".
- Left alone, needs the user: `<meta name=" ICB" content="5603467">` in app/layout.js (leading space, unknown purpose, added in 6ad3500).

## 2026-11-24: llms.txt rewritten, footer link

- `app/llms.txt` and `app/llms-full.txt` now share `lib/seo/llms-content.js` (intro, what the app does and doesn't do, tools, legal, attribution). Dropped the old filler and the false claims (web app, warranty-expiry reminders, export, Spaces, offline, email parsing, teams). Added maintenance reminders, service records and the product passport share link, checked in `ourbits_hmb_app`.
- Fixed legal URLs in both files (`/legal/privacy` and `/legal/terms` were 404s; real slugs are `privacy-policy`, `terms-of-use`). Support described as the contact form it is. llms-full now uses blog `excerpt` and hub `summary` (hub summaries were empty before).
- Footer: `llms.txt` link at the end of the Resources column (plain `<a>`, no prefetch).
- humanizer-v2 pass done; `npm run build` passes; both routes checked against `next start`.
- About intro: reminder claim replaced with "shows how much cover is left"; "every repair" now "its service history".
- Commits on develop: 3e89ea1 (llms + footer), 4a7e7e6 (About). Not pushed.
