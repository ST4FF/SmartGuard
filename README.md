# SmartGuard

SmartGuard is a security monitoring platform with a Django/Channels backend, a React/Vite operator UI, Redis-backed realtime events, PostgreSQL persistence, and a computer-vision detection worker.

## Repository layout

- `smartguard_backend/` — Django project, REST/WebSocket APIs, domain apps, migrations, and detection worker.
- `smartguard-frontend/` — React/Vite frontend for admin, operations, staff, alerts, cameras, evidence, and reports.
- `requirements.txt` — backend Python dependencies.
- `start_smartguard.bat` / `stop_smartguard.bat` — Windows convenience scripts; review paths before use.

## Local development

### Backend

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r smartguard_backend\requirements.txt
cd smartguard_backend
python manage.py migrate
python manage.py runserver 8000
```

Run the ASGI server and detection worker separately when testing WebSockets or camera processing:

```powershell
daphne smartguard_backend.asgi:application -p 8000
python detection_worker.py
```

### Frontend

```powershell
cd smartguard-frontend
npm install
Copy-Item .env.example .env.local
npm run dev
```

The Vite app normally runs on port `5173`. Set `VITE_API_BASE_URL`, `VITE_STREAM_BASE_URL`, and `VITE_RECAPTCHA_SITE_KEY` in `.env.local` as needed.

## Services and configuration

The backend expects PostgreSQL and Redis. Copy `smartguard_backend/.env.example` to `smartguard_backend/.env` and provide deployment-specific values, including `DJANGO_SECRET_KEY`, database credentials, Redis credentials, and any mail, Twilio, reCAPTCHA, or media-sync secrets used by the enabled features. Set `PGPASSWORD` before running `backup_db.sh`.

Never commit `.env` files, credentials, camera URLs, private keys, database dumps, build archives, or runtime logs. If a credential has ever been committed, rotate it before treating the repository as clean.

## Useful checks

```powershell
cd smartguard_backend
python manage.py check
python manage.py test
cd ..\smartguard-frontend
npm run lint
npm run build
```

## Deployment note

The included batch and shell scripts contain machine-specific paths and are convenience references, not portable deployment automation. Update paths and service names for the target host before running them.
