# Campus Care — Campus Maintenance Service

<img width="1899" height="992" alt="image" src="https://github.com/user-attachments/assets/5154e28d-c14f-431e-b802-8392d5ef1343" />


A calm, trustworthy web app for reporting and resolving campus maintenance issues.
Students report a problem, staff fix it, and the student confirms it with a clear
four-step trail (Reported → Assigned → Fixed → Confirmed) on every ticket.

## Roles

- **Student** (public — no login): report an issue, track it, confirm or reopen a fix.
- **Maintenance staff** (login): work orders for their department, upload after-photos.
- **Admin / Warden** (login): campus-wide oversight, reassignment, staff roles, audit log.

Only the signed-in role's portal is shown; students never see staff areas.

## Run locally

```bash
bun install
bun run dev          # http://localhost:3000  (Express + Vite, one port)
```

Staff accounts are provisioned from environment variables (never hardcoded). Copy
`.env.example` to `.env` and set `ADMIN_EMAIL` / `ADMIN_PASSWORD` and
`TECHNICIAN_EMAIL` / `TECHNICIAN_PASSWORD`. The admin console stays disabled with a
warning if `ADMIN_*` is unset. `GEMINI_API_KEY` is optional — without it the
deterministic fallback analyzer is used.

## Progressive Web App

- `public/manifest.webmanifest` + generated icons (192/512/maskable, Apple touch).
- `public/sw.js` service worker: offline app shell, stale-while-revalidate for
  hashed assets, network-first navigation, and `/api/*` always live.
- Registered in production builds (`src/main.tsx`). Install from the browser menu;
  it runs standalone and reopens offline.
- `theme-color` and `viewport-fit=cover` are set in `index.html`.

Verify after a production build:

```bash
bun run build
NODE_ENV=production bun run start   # then open http://localhost:3000
```

## Deploy to Render

`render.yaml` is a ready-to-use Blueprint:

1. Push this repo to GitHub/GitLab.
2. Render → **New → Blueprint** → select the repo.
3. Set the secret env vars (`ADMIN_EMAIL`, `ADMIN_PASSWORD`, `TECHNICIAN_EMAIL`,
   `TECHNICIAN_PASSWORD`, optional `GEMINI_API_KEY`) in the dashboard.
4. Render builds (`npm install --include=dev && npm run build`) and starts
   (`npm run start`), probing `/api/health`.

The server reads `PORT` from the environment and binds `0.0.0.0`, suitable for any
PaaS (Render, Fly, Railway). State is in memory for this demo, so a restart resets
tickets; point the stores at a database before production use.

## Checks

```bash
bun run lint    # tsc --noEmit
bun run test    # vitest (unit + auth/RBAC/rate-limit)
bun run build   # production bundle
bunx playwright test   # desktop / tablet / mobile e2e
```
