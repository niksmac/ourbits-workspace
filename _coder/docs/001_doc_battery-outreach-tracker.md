---
title: Battery passport outreach tracker (25 messages)
description: Ref-coded links for the 25 outreach messages, how visits, bookings and replies are counted, and the rule for promoting /battery-passport to the homepage.
repo: ourbits_io_website
status: open
createdDate: 2026-11-24
modifiedDate: 2026-11-24
---

# Battery passport outreach tracker

Plan: [001_plan_io-website-passport-repositioning](../plans/001_plan_io-website-passport-repositioning.md)

## How tracking works

- **Visits:** each message uses its own link, `https://ourbits.io/battery-passport?ref=bp-NN`. With `NEXT_PUBLIC_PLAUSIBLE_DOMAIN=ourbits.io` set in the hosting environment, Plausible records the page view with `bp-NN` as the source (Plausible reads `?ref=` natively) and a custom event `Outreach visit` with props `ref` and `page`. Without the env var nothing is sent, so visits are not counted.
- **CTA clicks:** `Readiness CTA` event with `position` and `ref`.
- **Bookings:** the readiness-check form posts to `POST https://api.holdmybill.com/v1/public/contact` with category `ourbits_partnership`. The email to hello@ourbits.io starts with `Passport readiness check (battery-passport)` and includes `Outreach ref: bp-NN`. The ref is kept for the browser session, so a visitor who reads a post first and books later is still attributed. Plausible also gets a `Lead submitted` event with `source` and `ref`.
- **Replies:** not visible to the site. Log them by hand in the table below.

Plausible setup: add the site `ourbits.io`, then add custom-event goals `Outreach visit`, `Readiness CTA`, `Lead submitted`, and the custom properties `ref`, `page`, `position`, `source`.

## Promotion rule

If 3 or more pilots say yes:

1. The battery H1 becomes the homepage hero (`src/components/Hero.jsx`), and "Product passports that keep working after the sale." drops to the subhead.
2. `/battery-passport` goes into `NAV_LINKS` in `src/components/Navbar.jsx` and into `src/app/sitemap.js`.
3. Remove `noIndex: true` from `src/app/battery-passport/page.js`.

## Tracker

Pilot = said yes to the 60-day pilot.

| Ref | Company | Contact | Channel | Link | Sent | Visited | Booked | Replied | Pilot |
|---|---|---|---|---|---|---|---|---|---|
| bp-01 | | | | `https://ourbits.io/battery-passport?ref=bp-01` | | | | |
| bp-02 | | | | `https://ourbits.io/battery-passport?ref=bp-02` | | | | |
| bp-03 | | | | `https://ourbits.io/battery-passport?ref=bp-03` | | | | |
| bp-04 | | | | `https://ourbits.io/battery-passport?ref=bp-04` | | | | |
| bp-05 | | | | `https://ourbits.io/battery-passport?ref=bp-05` | | | | |
| bp-06 | | | | `https://ourbits.io/battery-passport?ref=bp-06` | | | | |
| bp-07 | | | | `https://ourbits.io/battery-passport?ref=bp-07` | | | | |
| bp-08 | | | | `https://ourbits.io/battery-passport?ref=bp-08` | | | | |
| bp-09 | | | | `https://ourbits.io/battery-passport?ref=bp-09` | | | | |
| bp-10 | | | | `https://ourbits.io/battery-passport?ref=bp-10` | | | | |
| bp-11 | | | | `https://ourbits.io/battery-passport?ref=bp-11` | | | | |
| bp-12 | | | | `https://ourbits.io/battery-passport?ref=bp-12` | | | | |
| bp-13 | | | | `https://ourbits.io/battery-passport?ref=bp-13` | | | | |
| bp-14 | | | | `https://ourbits.io/battery-passport?ref=bp-14` | | | | |
| bp-15 | | | | `https://ourbits.io/battery-passport?ref=bp-15` | | | | |
| bp-16 | | | | `https://ourbits.io/battery-passport?ref=bp-16` | | | | |
| bp-17 | | | | `https://ourbits.io/battery-passport?ref=bp-17` | | | | |
| bp-18 | | | | `https://ourbits.io/battery-passport?ref=bp-18` | | | | |
| bp-19 | | | | `https://ourbits.io/battery-passport?ref=bp-19` | | | | |
| bp-20 | | | | `https://ourbits.io/battery-passport?ref=bp-20` | | | | |
| bp-21 | | | | `https://ourbits.io/battery-passport?ref=bp-21` | | | | |
| bp-22 | | | | `https://ourbits.io/battery-passport?ref=bp-22` | | | | |
| bp-23 | | | | `https://ourbits.io/battery-passport?ref=bp-23` | | | | |
| bp-24 | | | | `https://ourbits.io/battery-passport?ref=bp-24` | | | | |
| bp-25 | | | | `https://ourbits.io/battery-passport?ref=bp-25` | | | | |

**Totals:** sent 0 · visited 0 · booked 0 · replied 0 · pilots 0 / 3
