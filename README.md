# Nanotransfer

Deployment notes for **Render (backend)** + **Vercel (frontend)**.

## Backend (Render)

### Service settings
- **Root directory**: `backend`
- **Build command**: `pip install -r requirements.txt`
- **Start command**: `gunicorn app.main:app -k uvicorn.workers.UvicornWorker --bind 0.0.0.0:$PORT`
- **Health check path**: `/api/`

You can use the included `render.yaml` blueprint or set these in Render UI.

### Required environment variables
Copy `backend/.env.example` and set:
- `SECRET_KEY` (required)
- `DB_URL` (MySQL URL, e.g. `mysql+pymysql://user:pass@host:3306/db`)
- `FRONTEND_URL` (your Vercel URL, e.g. `https://your-app.vercel.app`)

Optional:
- `UPLOAD_DIR` (defaults to `uploads`)
- `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASSWORD`, `SMTP_FROM_EMAIL` (for invite emails)

## Frontend (Vercel)

### Project settings
- **Root directory**: `front-end`
- **Build command**: `npm run build`
- **Output directory**: `dist`

SPA rewrites are already configured in `front-end/vercel.json`.

### Required environment variables
Copy `front-end/.env.example` and set:
- `VITE_API_URL=https://your-backend.onrender.com/api`

## Free-tier deployment caveats

- Uploaded files are stored on local disk (`UPLOAD_DIR`). Render free filesystem is ephemeral, so files can be lost on restart/redeploy.
- For persistent uploads, use object storage (S3-compatible, Cloudinary, Supabase Storage, etc.).
- Backend DB driver is MySQL (`pymysql`). Use a MySQL-compatible hosted DB unless you also migrate code/driver for PostgreSQL.

## Quick post-deploy checks

1. Backend is up and `/docs` opens.
2. Frontend can call backend from Vercel domain.
3. Register/login works.
4. File upload/download and share links work.
5. Invite email flow works (if SMTP vars are set).
