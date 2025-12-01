# miniCRM

A small React + Express + MongoDB CRM demo with Google OAuth sign-in, campaign batch-sends, and delivery receipts.

---

## Overview

- Backend: Express, Passport (Google OAuth), MongoDB (Mongoose)
- Frontend: React (Vite), React Router, Axios
- Auth: Google OAuth using `passport-google-oauth20` with server-side sessions
- Intended for demo / small internal use. Not hardened for production by default.

---

## Features

- Google sign-in (server-side, sessions)
- CRUD for customers, orders, campaigns
- Campaign auto-tagging (basic heuristics + optional OpenAI)
- Simulated vendor that posts delivery receipts to backend

---

## Repo layout

- `Backend/` — Express server, routes, models, passport config
- `Frontend/` — Vite + React app

---

## Prerequisites

- Node.js 18+ (recommended)
- npm
- A MongoDB connection string (Atlas or self-hosted)
- Google OAuth 2.0 credentials (Client ID & Secret)

---

## Local development (quickstart)

1. Backend

- Copy environment variables into `Backend/.env` (or use `Backend/.env.development`):

```
PORT=5000
MONGO_URI=your_mongo_uri
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
SESSION_SECRET=some_long_secret
FRONTEND_URL=http://localhost:5173
BACKEND_URL=http://localhost:5000
GOOGLE_CALLBACK_URL=http://localhost:5000/auth/google/callback
VENDOR_SECRET=some_vendor_secret
```

- Install and run backend:

```powershell
cd Backend
npm install
npm run dev   # uses nodemon (or `npm start`)
```

2. Frontend

- Set `Frontend/.env` (or export env var) with API base URL:

```
VITE_API_URL=http://localhost:5000
```

- Install and run frontend:

```powershell
cd Frontend
npm install
npm run dev
```

Open `http://localhost:5173` in your browser.

---

## Environment variables (summary)

Backend (important vars):
- `PORT` — default `5000`
- `MONGO_URI` — MongoDB connection string
- `GOOGLE_CLIENT_ID` — Google OAuth client ID
- `GOOGLE_CLIENT_SECRET` — Google OAuth client secret
- `GOOGLE_CALLBACK_URL` — e.g. `http://localhost:5000/auth/google/callback` (must match Google console)
- `FRONTEND_URL` — frontend origin (used for redirects and CORS)
- `SESSION_SECRET` — session cookie secret
- `VENDOR_SECRET` — HMAC secret used by vendor simulation

Frontend:
- `VITE_API_URL` — e.g. `http://localhost:5000` or your production backend URL

Note: For production, set `NODE_ENV=production` and ensure `GOOGLE_CALLBACK_URL` is the full HTTPS backend callback.

---

## Google OAuth setup

1. In Google Cloud Console, create OAuth 2.0 credentials.
2. Under **Authorized JavaScript origins** add your frontend origin(s), e.g. `https://your-site.vercel.app` and `http://localhost:5173`.
3. Under **Authorized redirect URIs** add your backend callback(s), e.g. `https://backend-jdl7.onrender.com/auth/google/callback` and `http://localhost:5000/auth/google/callback`.
4. Use the client ID and client secret in your backend env.

Important: the redirect URI set in the Google console must exactly match `GOOGLE_CALLBACK_URL` used by `passport` in `Backend/config/passport.js`.

---

## Production notes (Render / Vercel)

- Backend (Render or similar):
  - Set `NODE_ENV=production`.
  - Set `FRONTEND_URL` to your Vercel frontend URL (e.g. `https://your-site.vercel.app`).
  - Set `BACKEND_URL` to your backend URL (e.g. `https://backend-jdl7.onrender.com`).
  - Set `GOOGLE_CALLBACK_URL` to `${BACKEND_URL}/auth/google/callback` and register that exact value in Google console.
  - Ensure `SESSION_SECRET`, `MONGO_URI`, `VENDOR_SECRET`, and `GOOGLE_*` variables are set.

- Frontend (Vercel):
  - Set `VITE_API_URL` to your backend URL (e.g. `https://backend-jdl7.onrender.com`).
  - Build and deploy normally.

- Cookie / Session cross-domain requirements:
  - If frontend and backend are on different top-level domains in production, session cookies must be served with `SameSite=None` and `Secure=true`.
  - The backend should be run behind HTTPS and `app.set('trust proxy', 1)` or the equivalent in your hosting environment if using a proxy.

---

## Troubleshooting

- Redirect to `/dashboard` on unknown route: see `Frontend/src/App.jsx` — the route `path="*"` currently redirects to `/dashboard`. If you prefer a login fallback, change to redirect to `/login`.

- Google OAuth works locally but not in production: common causes
  - `GOOGLE_CALLBACK_URL` used by your backend does not match the redirect URI registered with Google.
  - CORS or origin mismatch: add both frontend and backend origins to allowed origins and to Google console.
  - Session cookie not set in the browser because `SameSite` or `Secure` is incorrect for cross-site usage — in production `SameSite=None` and `Secure=true` are required when frontend and backend are on different domains.
  - `NODE_ENV` not set to `production` and proxy/trust settings missing behind a reverse proxy — enable `proxy: true` or `app.set('trust proxy', 1)` as needed.

- If you see `401` from `/auth/me`: verify the browser is sending cookies (`withCredentials: true` is set in the frontend axios client) and that backend CORS allows `credentials` from your frontend origin.

---

## Useful commands

Frontend:
```powershell
cd Frontend
npm install
npm run dev
```

Backend:
```powershell
cd Backend
npm install
npm run dev
```

---

## Security & next steps

- Rotate secrets before public deployment (do NOT check real secrets into git).
- Use HTTPS in production and set `secure: true` for cookies.
- Consider storing sessions in a persistent store (Redis) for multi-instance deployments.

---

## License

MIT-style license — feel free to adapt.
