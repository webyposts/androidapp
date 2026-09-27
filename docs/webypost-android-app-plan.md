# Webypost Android App — Complete Build Plan

**Product:** [webypost.com](https://webypost.com)  
**Goal:** Full Android app that keeps the existing MySQL backend, matches the current mobile-responsive look, and uses LinkedIn / Facebook-style in-between page transitions.  
**Build method:** Claude Code as primary implementer, with a human owner for review, device QA, and store setup.  
**Date:** 22 September 2026

---

## 1. What this document is

This is the end-to-end plan for turning Webypost into a native-feeling Android app without throwing away the live site or the MySQL database.

It covers:

- Product scope (v1 vs later)
- Why a WebView wrapper will not deliver the requested transitions
- Recommended stack (API + TypeScript)
- Backend / API contract
- App information architecture and motion
- Day count and Claude usage estimates
- Week-by-week Claude Code prompt plan
- Risks, Play Store work, and definition of done

Use this file as the root brief you paste into Claude Code at the start of the project (`CLAUDE.md` or `docs/ANDROID_PLAN.md`).

---

## 2. Current product (as of review)

Webypost is a profile-centric social home, not a generic clone of Facebook.

**Positioning**

- One profile as a living portfolio
- Short posts + long-form articles / News Trends
- Markets listings next to the voice
- Billboard Score™ as reputation

**Visible modules**

| Module | What users do |
|---|---|
| Auth | Email login / register, Google Sign-In, remember me, forgot password |
| Profile | Portfolio-style identity, interests, skills, aspirations, followers / following / posts |
| Social graph | Follow, recommend, message |
| Posts | Status, media, links, journals |
| Articles / News Trends | Read and publish long-form |
| Markets | Product and service listings by category |
| Billboard Score | Trust / activity / value / consistency style scores |
| Marketing pages | About, FAQs, Support, Contact, Terms, Privacy |

**Observed web stack**

- PHP pages + MySQL (stated backend)
- AngularJS 1.8.3 on at least the marketing / auth surface
- Google Identity Services
- Fonts: Poppins + Carlito
- Brand: deep green header/hero, yellow accent, white cards, rounded login card
- Cloudflare in front
- Mobile site already exists and is the visual source of truth

**Implication:** the website is multi-page / server-rendered with some AngularJS. That is fine for web. It is the wrong navigation model for “LinkedIn / Facebook transitions.” Those apps keep a persistent chrome (tabs, header) and *animate screens*, they do not full-reload HTML.

---

## 3. The real requirement, restated

You asked for four things at once:

1. Same MySQL backend
2. Pages connected by API / TypeScript (or equivalent)
3. Same look as the current mobile-responsive site
4. Full Android app with smooth in-between page transitions

That means:

- **Do not scrape or WebView the PHP pages as the primary UI**
- **Do extract a JSON API** in front of the same tables
- **Do rebuild screens in a native navigation stack** that *looks* like the mobile web
- **Do invest in motion** (shared elements, tab persistence, gesture back)

A Trusted Web Activity or Capacitor wrap can ship in days. It will not feel like LinkedIn or Facebook between pages unless the site is first rebuilt as a true SPA. That is a different project.

---

## 4. Recommended architecture

```
┌─────────────────────────────────────────────┐
│ Android app (React Native + Expo + TS)      │
│  - React Navigation + Reanimated 3          │
│  - TanStack Query + SecureStore             │
│  - Google Sign-In (Android client)          │
└──────────────────────┬──────────────────────┘
                       │ HTTPS JSON + JWT
                       │ multipart uploads
┌──────────────────────▼──────────────────────┐
│ API layer (new) on same server or subdomain │
│  api.webypost.com  or  webypost.com/api/v1  │
│  PHP (Slim / Laravel) or Node if preferred  │
└──────────────────────┬──────────────────────┘
                       │
┌──────────────────────▼──────────────────────┐
│ Existing MySQL  +  existing file storage    │
│ Do not migrate data in v1                   │
└─────────────────────────────────────────────┘
```

### Why React Native + Expo + TypeScript

- Matches the “API / TypeScript” preference
- Claude Code is strongest in TS/JS
- Expo speeds Android builds, icons, splash, EAS Submit
- Reanimated + Gesture Handler can do shared-element and stack transitions
- One design-token file can mirror the current mobile CSS

### Alternatives (only if you have a strong reason)

| Option | Use when | Cost |
|---|---|---|
| Flutter + Dart | You want the most “native” motion and one codebase later for iOS | Claude is slightly weaker than on TS |
| Kotlin + Compose | You want a pure Android app and will maintain it long-term | Slowest for an AI-first build |
| Capacitor / Ionic around current PHP | You only need a store listing, not LinkedIn-like motion | Fast, wrong feel |
| PWA + TWA | Zero extra backend | Fastest, still a website |

**Recommendation:** React Native + Expo for v1. Revisit Kotlin only if Play performance or motion hits a wall.

### API language

Keep the API in **PHP** if that is what the current site is written in. Claude Code can generate Slim 4 or a thin Laravel module next to the existing app. Do not rewrite MySQL access in another language unless the current PHP is unmaintainable.

A Node/TypeScript API is acceptable if you want one language across API and app, but it adds a second runtime on the server.

---

## 5. Design rule: same look, different navigation

Copy from the mobile website:

- Green `#0B5C4A`-family header / hero (match the live hex from CSS, do not guess in production)
- Yellow / gold accent on logo and links
- Poppins for UI, Carlito if used for body/headings
- White rounded cards, soft shadow, large tap targets
- Login / Register segmented control
- Profile header: avatar, name, handle, follow counts, Billboard, Message / Recommend
- Feed cards: avatar, name, time, body, media, engagement row

Do **not** copy from the website:

- Full page reloads
- Hamburger-only primary nav as the only way to move
- Separate HTML document per screen

App chrome (LinkedIn / Facebook pattern):

```
┌─────────────────────────────┐
│ Top bar (context-aware)     │
├─────────────────────────────┤
│                             │
│  Screen content             │
│  (stack, shared elements)   │
│                             │
├─────────────────────────────┤
│ Home  Search  Write  Inbox  │
│ Me                          │
└─────────────────────────────┘
```

Suggested tab set (adjust names to current IA):

1. **Home** — feed
2. **Discover** — search / News Trends / people
3. **Write** — compose post or article (center action)
4. **Markets** — listings
5. **Me** — own profile + Billboard + settings

Messaging can live as a stack screen from profile or as a later 6th surface.

---

## 6. Scope

### 6.1 v1 — ship this (MVP that still feels like an app)

Must have:

- Splash + session restore
- Email login / register / logout / forgot password
- Google Sign-In on Android (new OAuth client; web client ID is not enough)
- Home feed with pagination and pull-to-refresh
- Open post detail
- Compose text post + image upload
- Profile view (self + other)
- Follow / unfollow
- Billboard Score read-only on profile
- Articles list + article reader
- Markets list + listing detail (no checkout)
- Search (people + posts, even if simple)
- Settings: account, logout, legal links
- Persistent bottom tabs
- Stack push / pop with gesture back
- Shared-element transition on avatar Home → Profile and Feed → Profile
- Error / empty / offline basic states
- Deep link for `https://webypost.com/{username}` and post URLs
- Android 8+ (API 26+), phones first
- Play Console closed testing build

Explicitly out of v1:

- In-app chat thread UI (Message button can open a simple compose or “coming soon”)
- Markets cart / payments / shipping
- Full article WYSIWYG editor (link out to web editor if needed)
- Admin tools
- iOS
- Pixel-perfect clone of About / FAQ / Support as native screens (open WebView or in-app browser)
- Every legacy PHP page

### 6.2 v1.5

- Comments + likes wired to real tables
- Push notifications (FCM)
- Recommend flow
- Article publish from app (Markdown is enough)
- Markets “contact seller”

### 6.3 v2

- Real messaging
- Markets checkout if the web already sells
- Video posts
- Rich Billboard breakdown screen
- Tablet layout
- iOS with the same Expo project

Do not let Claude Code start v2 screens during v1. That is how timelines explode.

---

## 7. Backend work (same MySQL)

### 7.1 First job before any app screen

1. Export schema (`mysqldump --no-data`)
2. List every table used by auth, profiles, posts, follows, articles, markets, scores, media
3. Grep the PHP / AngularJS code for existing XHR / `fetch` / `$http` endpoints
4. Write OpenAPI 3 for `/api/v1`
5. Add an API user / app key strategy

If undocumented AJAX already returns JSON, wrap and stabilize it. Do not invent a second user table.

### 7.2 Suggested API prefix

`https://webypost.com/api/v1`  
or  
`https://api.webypost.com/v1`

Version it. The website can keep using old form posts.

### 7.3 Auth

- `POST /auth/register`
- `POST /auth/login`
- `POST /auth/google`
- `POST /auth/forgot`
- `POST /auth/reset`
- `POST /auth/refresh`
- `POST /auth/logout`
- `GET  /me`

Use short-lived access JWT + refresh token stored in Android `SecureStore`.  
Passwords stay hashed as they are today.  
Google: verify ID token server-side, then link or create the existing user row.

### 7.4 Core resources (minimum)

```
GET    /feed?cursor=
POST   /posts
GET    /posts/{id}
DELETE /posts/{id}          (owner only)

GET    /users/{handle}
PATCH  /me
POST   /users/{id}/follow
DELETE /users/{id}/follow
GET    /users/{id}/followers
GET    /users/{id}/following

GET    /articles?cursor=
GET    /articles/{id}

GET    /markets?category=&cursor=
GET    /markets/{id}

GET    /search?q=&type=people|posts|articles|listings

GET    /users/{id}/billboard

POST   /uploads             (multipart, returns URL + id)
```

Add likes / comments only when the tables already exist and v1 feed is stable.

### 7.5 API rules Claude must follow

- JSON in / JSON out
- Cursor pagination, not `page=1` offset, for feeds
- Consistent error body: `{ "error": { "code", "message" } }`
- Auth on every non-public route
- Rate limit login and search
- Do not return password hashes, reset tokens, or internal IDs you do not need
- CORS allow the Expo dev origin and later the app (apps do not use CORS the same way, but the API will also be called from web tools)
- File uploads: size cap, MIME allow-list, store where the website already stores images
- Never grant the Android app raw MySQL credentials

### 7.6 Do not

- Create a parallel database
- Change column names the website depends on
- “Modernize” the whole PHP site in the same sprint

---

## 8. Android app structure

Suggested Expo / RN layout:

```
apps/mobile/
  app.json
  src/
    app/
      navigation/
        RootNavigator.tsx
        TabNavigator.tsx
        linking.ts
      providers/
        AuthProvider.tsx
        QueryProvider.tsx
    features/
      auth/
      feed/
      profile/
      compose/
      articles/
      markets/
      search/
      settings/
    shared/
      ui/          # Button, Card, Avatar, TopBar...
      theme.ts     # colors, type, space from the live site
      api/         # typed client
      motion/      # shared transition presets
    assets/
```

Libraries (keep the set small):

- `expo` + `expo-router` **or** React Navigation 7 (pick one and stay)
- `react-native-reanimated`
- `react-native-gesture-handler`
- `react-native-safe-area-context`
- `@tanstack/react-query`
- `zod` for API response validation
- `expo-secure-store`
- `expo-image` / `expo-image-picker`
- `@react-native-google-signin/google-signin`
- `expo-notifications` (v1.5, not day one)

### Motion spec (the actual “LinkedIn / Facebook” part)

Implement these five transitions and stop:

1. Tab switch: fade + slight scale, tabs themselves never unmount feed state
2. Stack push: 30–40% slide from right, previous screen parallax
3. Gesture back: interactive pop
4. Avatar shared element: feed / search → profile
5. Compose: modal from bottom (80–90% sheet)

Do not animate every list row. Over-animation is how cheap apps feel.

Feed must keep scroll position when you open a profile and come back. That single detail matters more than decorative Lottie.

---

## 9. Time estimate

Assumptions:

- One person directing Claude Code
- Existing schema access
- v1 scope in section 6.1 only
- Real phone testing every day
- No payments, no full chat

### Calendar

| Phase | Work | Working days |
|---|---|---|
| 0. Discovery | Schema dump, endpoint inventory, mobile screenshot pack, OpenAPI skeleton | 3–5 |
| 1. API | Auth + feed + profile + follow + articles + markets read + uploads | 10–14 |
| 2. App shell | Expo, theme, tabs, auth stack, motion presets | 4–6 |
| 3. Screens | Feed, post, profile, compose, articles, markets, search, settings | 18–25 |
| 4. Polish | Transitions, empty/error, image cache, deep links | 7–10 |
| 5. Ship | Keystore, Play Console, privacy form, closed test | 5–8 |
| **v1 total** | | **45–65 working days (6–9 weeks full-time)** |

If you only work 4–5 focused hours/day: **10–14 weeks**.

Full parity (chat, commerce, every PHP page, iOS): **4–6 months**.

WebView wrapper of the current site: **7–12 days**, wrong motion.

### What “days” means

A “working day” here is a directed Claude Code day: spec in the morning, generate, run on device, patch, commit. It is not “leave Claude overnight and ship.”

---

## 10. Claude usage estimate

Two different percentages people mix up.

### 10.1 Share of work Claude can generate

| Area | Claude-generated | Human-owned |
|---|---|---|
| CRUD API, types, screens, lists | 75–85% | Review diffs |
| Visual match to mobile web | 50–70% | Side-by-side screenshots on a phone |
| Navigation + shared-element motion | ~60% | Feel it with a thumb |
| Auth, uploads, token storage | ~40% | Security pass |
| Play Console, OAuth clients, signing | 20–30% | You click the consoles |
| **v1 overall** | **~70% code** | **~30% judgment / QA / store** |

Claude Code should be treated as a very fast mid-level engineer who does not see the phone unless you describe what is wrong.

### 10.2 Subscription / quota burn

Claude Code shares the same pool as claude.ai. Limits are a rolling **5-hour window** plus a **weekly cap**. Published bands (2026):

| Plan | Rough session capacity | Rough weekly Claude Code (Sonnet) |
|---|---|---|
| Pro (~$20) | Tight for this repo | You will stall most days |
| Max 5x (~$100) | ~50–200 prompts / 5h | ~140–280 hours/week Sonnet |
| Max 20x (~$200) | ~200–800 prompts / 5h | ~240–480 hours/week Sonnet |

Opus burns the weekly cap much faster than Sonnet.

**For this project**

| Plan | Expected quota use while building | Verdict |
|---|---|---|
| Pro | 100% most days | Adds months |
| Max 5x | 70–100% of weekly cap if you use Opus + large context | Possible if you stay on Sonnet for screens |
| Max 20x | 40–70% of weekly cap in peak weeks | Best fit for a 6–9 week v1 |
| API pay-as-you-go | No weekly cap | Budget a few hundred USD extra if Max throttles |

“Claude usage % to complete the project” on **Max 20x** is not 100% of one month. It is **about half to two-thirds of each week’s cap, for 8–12 weeks**.

### 10.3 How to spend quota

- Sonnet for screens, lists, styling, tests
- Opus only for API auth design, schema mapping, and the motion layer
- One Claude Code session per feature (`feed`, `auth`, `profile`), not one immortal chat
- Do not paste the entire PHP site into context
- Commit after every green screen
- Put this plan + OpenAPI + theme tokens in `CLAUDE.md` so every new session starts informed

---

## 11. Week-by-week plan (Claude Code)

Work in this order. Do not skip week 1.

### Week 1 — Freeze the contract

Human + Claude:

- Dump schema
- Screenshot every mobile web screen into `docs/screens/`
- Write `docs/openapi.yaml` for v1 endpoints
- Write `CLAUDE.md` with stack rules and “do not rewrite the website”
- Create Expo app `apps/mobile` and empty API folder `api/v1`

Done when: OpenAPI lists every v1 route and status codes.

### Week 2–3 — API

Claude implements PHP (or Node) routes against **existing tables**.

Order:

1. Health check
2. Auth email
3. `/me`
4. Profile by handle
5. Feed
6. Create post + upload
7. Follow
8. Articles list/detail
9. Markets list/detail
10. Search

Done when: Postman / Bruno collection is green against staging MySQL.

### Week 4 — App shell + auth

- Theme tokens copied from live CSS
- Auth stack: login / register / forgot
- Google Sign-In Android client wired
- SecureStore session
- Tab navigator with placeholder screens
- Motion presets file

Done when: you can log in on a physical device and see five tabs.

### Week 5–6 — Feed + profile + compose

This is the product.

- Feed list + post detail
- Shared-element avatar to profile
- Follow button
- Billboard block
- Compose sheet + image picker
- Scroll position preserved

Done when: a new user can post a photo and see it on their profile.

### Week 7 — Articles + Markets + Search

- Read-only articles
- Read-only listings
- Search people / posts
- Deep links

Done when: Discover and Markets are usable, even if empty states are plain.

### Week 8 — Polish + store

- Empty / error / retry
- Image caching
- Back gesture everywhere
- App icon, splash, package name `com.webypost.app` (or your existing namespace)
- Privacy policy URL (already on the site)
- EAS build → internal testing track
- Crash reporting (Sentry or Firebase Crashlytics)

Done when: three people on the closed track can use auth + feed + profile without a blocker.

If week 8 slips, cut Search polish and Markets filters. Do not cut motion or session restore.

---

## 12. Claude Code starter instructions

Put this in `CLAUDE.md` at the repo root:

```md
# Webypost

This repo contains the live PHP/MySQL site and a new Android client.

Rules:
- Do not replace the website in v1.
- Do not create a new database. Use existing MySQL tables.
- All app traffic goes through /api/v1 JSON. No HTML scraping.
- Mobile UI must match the current mobile-responsive look (colors, type, cards).
- Navigation is native: persistent tabs + stack + shared-element avatars.
- TypeScript strict in apps/mobile.
- Ask before changing schema.
- Auth and file uploads need explicit review before merge.
- v1 scope is listed in docs/webypost-android-app-plan.md. Do not build chat or payments unless asked.
```

Paste **one phase at a time**. Example prompt for week 4:

> Read `docs/webypost-android-app-plan.md` section 8 and 11 week 4.  
> Implement only the Expo app shell and email auth against `/api/v1`.  
> Use theme tokens from `src/shared/theme.ts`.  
> Do not build feed or markets in this session.

---

## 13. Play Store and ops (Claude cannot finish these alone)

Checklist:

- [ ] Google Play Developer account (already paid or new)
- [ ] Package name chosen and never changed
- [ ] Upload key + Play App Signing
- [ ] `google-services` / SHA-1 + SHA-256 on the Google Cloud OAuth client
- [ ] Privacy policy and terms URLs (exist on web)
- [ ] Data safety form (account data, photos, optional location = no unless you add it)
- [ ] Account deletion path (Play policy for social apps)
- [ ] Content moderation contact if users can post
- [ ] Target API level required by Play at ship date
- [ ] Closed testing → open testing → production

Social apps get extra policy scrutiny. Do not submit with a broken delete-account flow.

---

## 14. Risks

| Risk | Why it happens | Mitigation |
|---|---|---|
| “Same pages” interpreted as WebView | Fastest path for Claude | Ban WebView except legal pages |
| Schema is messy / undocumented | 2016-era PHP is common | Week 1 dump + mapping table |
| Google login works on web, fails in app | Different OAuth client + SHA fingerprints | Do Android Google login in week 4, not week 8 |
| Feed feels janky | Images unbounded, no windowing | FlashList / Recycler-style lists only |
| Claude rewrites the website | Prompt too broad | `CLAUDE.md` rule + small sessions |
| Quota exhaustion | Opus + huge context | Sonnet default, Max 20x, feature-sized chats |
| Scope creep (chat + payments) | Markets and Message buttons exist | Buttons can exist; flows wait for v2 |
| Visual drift | AI invents a new design system | Screenshot pack is source of truth |

---

## 15. Success criteria for v1

The app is done enough to list when all of the following are true:

1. A new user can register or Google-login on a real Android phone
2. Home feed loads from MySQL through `/api/v1`, not from a WebView
3. Opening a profile from the feed uses a shared-element avatar and gesture-back returns to the same scroll offset
4. Tabs do not remount the feed
5. User can publish a text + image post that also appears on the website
6. Profile, articles list, and a Markets detail page render with the current brand look
7. No plaintext passwords or DB credentials in the app binary
8. Closed testing build installed by people who are not the developer
9. Account logout and account-deletion path exist

If item 3 or 4 fails, it is not the app you asked for, even if every page exists.

---

## 16. Cost picture (non-Claude)

- Play Developer: $25 one-time (if not already paid)
- Claude Max 20x: ~$200 × 2–3 months typical for v1
- Expo EAS: free tier may suffice for a few Android builds; budget EAS Production if builds queue
- Optional Sentry / Firebase: free tier is enough at the start
- No need for a new server if API lives next to the current PHP

---

## 17. Decision summary

| Decision | Choice |
|---|---|
| Backend data | Existing MySQL, no migration in v1 |
| Integration | New `/api/v1` JSON over the same tables |
| App | React Native + Expo + TypeScript |
| Look | Current mobile website |
| Feel | Native tabs + stack + 5 named transitions |
| Builder | Claude Code ~70% of code, human 100% of ship responsibility |
| Calendar | 45–65 full-time working days for v1 |
| Quota | Max 20x preferred; expect 40–70% weekly use for 8–12 weeks |
| Not in v1 | Chat, payments, iOS, website rewrite |

---

## 18. Immediate next actions

1. Put this file in the repo as `docs/webypost-android-app-plan.md`
2. Copy section 12 into `CLAUDE.md`
3. Export MySQL schema and drop it in `docs/schema.sql`
4. Capture a mobile screenshot pack of Home, Login, Profile, Feed (logged-in), Article, Markets, Search
5. Decide package name and API host
6. Start Week 1 only

Do not ask Claude Code to “build the whole Android app” in one prompt. That is how you get a WebView and a six-month cleanup.
