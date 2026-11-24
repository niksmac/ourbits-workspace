# AGENTS.md (Ourbits workspace)

This folder is a workspace. It holds four separate git repos that make up the Ourbits / HoldMyBill product. Work is started from here, but every code change lands in exactly one of the repos below.

The workspace root is its own small git repo that tracks only workspace files (`AGENTS.md`, `CLAUDE.md`, `.gitignore`). The four sub-repos are listed in `.gitignore` and are not tracked here: no submodules, no nested commits. When you clone a new sibling repo into this folder, add it to `.gitignore` and to the table below.

## Hard rule: one repo per task

- **Edit only one repo per task.** Before the first edit, name the repo you are going to change. Do not create, edit, delete, format, install dependencies, or run git write commands (commit, branch, push, stash, reset) in any other repo.
- **Editing a second repo needs explicit permission** from the user in the current conversation ("also update the app", "change both api and web"). Permission given for one task does not carry over to the next.
- **Reading is always allowed.** Read, grep, and inspect any repo to understand a contract or check impact.
- **If a change crosses a repo boundary** (see "Contracts" below): finish the change in the current repo, then stop and tell the user what the other repo needs (file, endpoint, field, route) and ask whether to do it. Do not make the second change on your own.
- **No edits at the workspace root** other than `AGENTS.md`, `CLAUDE.md` and `.gitignore`. Never add a root `package.json`, lockfile or workspace config, never turn the sub-repos into submodules, and never `git add` a sub-repo folder in the root repo.

## Repos

| Repo | What it is | Stack | Remote | Working branch |
|---|---|---|---|---|
| `ourbits_hmb_api/` | HoldMyBill backend: REST API (`/v1`), Bull worker, hmb-mcp (MCP + OAuth server) | Node 24, Fastify (CommonJS), MongoDB/Mongoose, Redis/Bull, yarn | `lightrainstech/ourbits_hmb_api` | `develop` (push to `master` deploys to EC2) |
| `ourbits_hmb_app/` | HoldMyBill mobile app (iOS/Android) | Flutter, Riverpod, Dio, Freezed | `lightrainstech/holdmybill-app` | `master` |
| `ourbits_hmb_web/` | holdmybill.com: product marketing site, blog, guides, tools, legal pages, email-link landing pages | Next.js 16 App Router, React 19, Tailwind 4, plain JS, yarn | `lightrainstech/ourbits_hmb_web` | `develop` (default `master`) |
| `ourbits_io_website/` | Ourbits Systems company site (features HoldMyBill as a product) | Next.js 16, plain JS, local MDX blog | `ourbits-systems/ourbits_io_website` | `develop` |

Each repo has its own rules, and they apply inside that repo: `ourbits_hmb_api/AGENTS.md`, `ourbits_hmb_app/AGENTS.md`, `ourbits_hmb_web/AGENTS.md`, and `ourbits_io_website/README.md` (no AGENTS.md yet). Read the target repo's file before editing it. Where it is stricter than this file (for example the app's planning and API gates), follow it.

Run git and package-manager commands from inside the repo (`cd ourbits_hmb_api && git status`). Git run at the workspace root acts on the workspace repo, not on a sub-repo.

## How they connect

```
                    ┌──────────────────────────┐
                    │ ourbits_hmb_api          │
                    │ api.holdmybill.com /v1   │
                    │ passport.holdmybill.com  │
                    │ mcp/auth.holdmybill.com  │
                    └─────▲──────────▲───┬─────┘
          REST via Dio,   │          │   │ emails link to
          OpenAPI spec    │  fetch   │   │ /verify, /reset-password,
                          │ (CORS)   │   │ /legal/*, /support, /about
┌─────────────────┐       │   ┌──────┴───▼─────────┐        ┌────────────────────┐
│ ourbits_hmb_app │───────┘   │ ourbits_hmb_web    │◄───────│ ourbits_io_website │
│ Flutter app     │◄──────────│ holdmybill.com     │ links  │ ourbits company    │
└─────────────────┘ deep links└────────────────────┘        └────────────────────┘
                    (.well-known)
```

`ourbits_hmb_api` is the hub. The app and the web auth pages depend on it; the web repo also hosts the app's deep-link files; the company site only links out.

## Contracts (check these before changing either side)

### api ↔ app

- App base URL: `API_BASE_URL` in the app's `.env`, falling back to `https://api.holdmybill.com` (`ourbits_hmb_app/lib/core/network/dio_provider.dart`). Every path is `/v1/...`.
- The app integrates against a cached OpenAPI spec: `ourbits_hmb_app/_coder/openapi.holdmybill.json`, from the API's Swagger JSON at `https://api.holdmybill.com/docs/json` (version at `.info.version`). The API generates it from `src/payloads` + `src/config/swagger`.
- Shipped app versions keep calling old shapes until users update (store review adds lag). API changes to paths, request bodies, response fields, enums, or the `reply.success`/`reply.error` envelope must stay backward compatible, or be agreed with the user first.
- Push notifications (FCM/APNs), Apple/Google sign-in, and in-app purchase verification (`webhooks.js`) use bundle IDs and keys configured on both sides (`APPLE_BUNDLE_ID`, `ANDROID_PACKAGE_NAME`, `GOOGLE_CLIENT_ID_*` in the API `.env`).

### api ↔ web

- API emails build links from `PROJECT_FQDN`: `/verify/:userId?o=<token>&next=dashboard` and `/reset-password/:userId?token=<token>` (`ourbits_hmb_api/src/services/v1/user.js`). The web pages `app/verify/[id]/page.js` and `app/reset-password/[id]/page.js` handle them.
- Those web pages call the API from the browser with hardcoded URLs: `POST https://api.holdmybill.com/v1/user/validate` and `.../v1/user/reset-password`. They work only while the API's CORS allows `https://holdmybill.com` (`ALLOWED_ORIGINS`, default in `src/app.js`).
- Email templates (`ourbits_hmb_api/email-templates/includes/*.pug`) link to web routes `/about`, `/legal/privacy-policy`, `/legal/terms-of-use`, `/support`, and the asset `/images/logo_thumbnail.png`. Renaming or moving any of these in the web repo breaks emails already sent; add a redirect in `next.config.mjs` instead.

### web ↔ app

- `ourbits_hmb_web/public/.well-known/apple-app-site-association` (app ID `HN6AB3U28L.com.ourbitssys.holdmybill`, paths `/reset-password/*`, `/verify/*`) and `assetlinks.json` make the email links open the app. The Android side is the `holdmybill.com` intent filter in `ourbits_hmb_app/android/app/src/main/AndroidManifest.xml` (currently `/reset-password` only). No associated-domains entitlement exists under `ourbits_hmb_app/ios/` yet, so iOS universal links are not wired on the app side.
- The app opens `https://holdmybill.com/legal/privacy-policy` and `/legal/terms-of-use`. `/download` (and `/app`) on the web sends visitors to the right store.

### Other API-owned domains

- `passport.holdmybill.com/p/:token`: public product-passport / share pages, served by `ourbits_hmb_api/src/services/v1/public.js` (`PASSPORT_DOMAIN`).
- `mcp.holdmybill.com/mcp` and `auth.holdmybill.com`: hmb-mcp in `ourbits_hmb_api/src/mcp/`. `ourbits_io_website` links to it.

### io_website → everything else

- Links to `holdmybill.com` (and `/mcp`) only. No code or API dependency. Changes here never need changes elsewhere, and vice versa, except when a linked holdmybill.com URL changes.

### Outside this workspace

- `ourbits_hmb_web/lib/claims/core/` is vendored from the `warranty_claim_plugin` repo, which is not in this folder. Do not edit it here (see `ourbits_hmb_web/AGENTS.md`).

## Picking the repo

- Endpoint, data model, job, email, push, purchase verification, MCP → `ourbits_hmb_api`
- Mobile screen, app flow, app store build → `ourbits_hmb_app`
- holdmybill.com page, blog, guides, calculators, legal text, verify/reset pages, deep-link files → `ourbits_hmb_web`
- ourbits company site → `ourbits_io_website`

If a request is ambiguous ("fix the reset password flow"), check the contract above, say which repo you will edit and why, and name any follow-up the other repos may need.

## Verification per repo

- api: no test suite; lint with `npx @biomejs/biome check <path>`. Don't run `scripts/` (they hit real Mongo/Redis).
- app: `flutter analyze` (plus the repo's own gates).
- web: `npm run build` is the only working check (lint is broken by design).
- io_website: `npm run build`.
