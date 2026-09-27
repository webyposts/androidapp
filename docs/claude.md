# WebyPost Android

Native React Native + Expo (TypeScript) client for webypost.com.
This repo is the MOBILE APP ONLY. The API lives in the PHP website repo.

## Hard rules
- Do NOT rewrite or scrape the website. All data comes from /api/v1 JSON.
- Do NOT create a new database or a second user/auth table. Reuse existing MySQL via the API.
- No MySQL credentials, admin secrets, or DB access anywhere in this repo.
- TypeScript strict. No `any` without a written reason.
- All network calls go through src/api/ (typed client). No raw fetch/axios in screens.
- Match the webypost look: use src/theme tokens (colors, Poppins/Carlito, radius, spacing) pulled from the live CSS. Do not invent a new design system.
- Navigation is native: persistent bottom tabs + native stack + gesture back.
- Ask before: schema changes, deleting code, changing auth, irreversible architecture calls.

## Build order (do not skip ahead)
Phase 0 audit → API contract → auth+feed API green in Postman → app shell → feed on device → then profile/post/compose/notifications.

## v1 scope
auth, feed (cursor pagination), post detail, profile, compose text+image, follow, notifications, articles read, search. NO chat, NO payments, NO iOS in v1.
