# Traffic Exchange — M1 (Full Working Scaffold)

This is a **real, ethical** traffic-exchange MVP (Milestone 1) you can run today.

- Frontend: React + Vite + Firebase Auth (email/password), Add Site, Surf UI with timer, Dashboard placeholder.
- Backend: Node.js + Express + Firebase Admin (ID token verify), in-memory site queue (simple), endpoints.
- Deploy: Frontend (Netlify), Backend (Railway). Redirects included.
- Supabase migrations included for future M2 (credits ledger + persistent visits).

> M1 intentionally stores sites **in-memory** (resets on server restart). M2 will wire Supabase for persistence + credits.

---

## 1) Firebase (required)
1. Go to Firebase Console → create project → add **Web App**. Copy:
   - apiKey, authDomain, projectId
2. Authentication → **Email/Password** → Enable.
3. Project Settings → Service accounts → **Generate new private key** → Download JSON.
   - You'll paste values into backend `.env` (see below).

---

## 2) Frontend (local dev)
```bash
cd frontend
cp .env.example .env.local
# open .env.local and fill:
# VITE_FIREBASE_API_KEY=...
# VITE_FIREBASE_AUTH_DOMAIN=...
# VITE_FIREBASE_PROJECT_ID=...
npm install
npm run dev
# open http://localhost:5173
```

### Local API proxy
`vite.config.js` already proxies `/api` → `http://localhost:4000`

---

## 3) Backend (local dev)
```bash
cd backend
cp .env.example .env
# Fill the Firebase Admin & optional Supabase values
npm install
npm run dev
# backend on http://localhost:4000
```

**.env required**
```
PORT=4000
FIREBASE_ADMIN_PROJECT_ID=your-firebase-project-id
FIREBASE_ADMIN_CLIENT_EMAIL=from-service-account@...gserviceaccount.com
# Keep quotes and 
 escapes
FIREBASE_ADMIN_PRIVATE_KEY="-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----\n"

# Optional for future M2
SUPABASE_URL=https://xyz.supabase.co
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key
```

---

## 4) Connect + Test (local)
- Start backend (4000) and frontend (5173).
- Sign up (Firebase), then **Add Site** (URL, Country, Min Time).
- **Surf** → timer completes → backend logs `/api/surf/report`.

---

## 5) Deploy
### Frontend → Netlify
- New site from your repo (root), but set build to `frontend`
  - Build command: `npm run build`
  - Base directory: `frontend`
  - Publish directory: `frontend/dist`
- Add env vars:
  - `VITE_FIREBASE_API_KEY`, `VITE_FIREBASE_AUTH_DOMAIN`, `VITE_FIREBASE_PROJECT_ID`
- Update `netlify.toml` redirect `to = "https://YOUR-BACKEND-URL/:splat"` after backend deploy.

### Backend → Railway
- New service from GitHub (select **backend/**) or deploy via Dockerfile.
- Add env vars from `.env` (Firebase admin values).
- Copy the **public backend URL** and update Netlify redirect.

---

## 6) Supabase (optional in M1, needed in M2)
Open Supabase SQL editor and run `supabase_migrations/001_init.sql`

---

## 7) Notes
- Country targeting: M1 filters assignment by `site.country` (exact match or "any").
- Anti-abuse: timer + different owner; full checks (focus/visibility/IP rate-limits) planned for M2.
- Iframe fallback: most sites will load in iframe; later we can add "open new tab" fallback if blocked.

Good luck! If anything breaks, tell me exactly which step and the error message.
