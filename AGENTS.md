# AGENTS Guide for AIFriends

## Big Picture
- This repo is a split-stack app: Django backend in `backend/` + Vue 3 (Vite) frontend in `frontend/`.
- Django currently serves both API endpoints and the built SPA shell (`backend/web/templates/index.html`).
- Frontend production build is emitted into Django static files (`frontend/vite.config.js` -> `../backend/static/frontend`).
- Root URL flow: `backend/backend/urls.py` -> `backend/web/urls.py` -> `index` view (`backend/web/views/index.py`) -> template mounts Vue at `#app`.

## Service Boundaries and Data Flow
- API auth endpoints are provided by SimpleJWT in `backend/web/urls.py`:
  - `api/token/`
  - `api/token/refresh/`
- REST framework auth is globally set to JWT Bearer tokens in `backend/backend/settings.py` (`REST_FRAMEWORK`, `SIMPLE_JWT`).
- CORS is enabled for frontend dev origin `http://localhost:5173` (`CORS_ALLOWED_ORIGINS`).
- Static/media serving in development is wired in `backend/backend/urls.py` when `DEBUG=True`.

## Developer Workflows (Observed)
- Frontend dev server (from `frontend/`): `npm install`, `npm run dev` (`frontend/package.json`).
- Frontend production build (from `frontend/`): `npm run build` writes into Django static dir.
- Django commands (from `backend/`): `python manage.py runserver`, `python manage.py migrate`, `python manage.py createsuperuser`.
- Node engine target is constrained in `frontend/package.json` (`^20.19.0 || >=22.12.0`).
- `backend/requirements.txt` is currently empty; install deps based on imports (`django`, `djangorestframework`, `djangorestframework-simplejwt`, `django-cors-headers`).

## Project-Specific Conventions
- Vue uses alias `@` for `frontend/src` (`frontend/vite.config.js`); prefer it for component imports.
- Current UI composition is navbar-first (`frontend/src/App.vue` renders `NavBar` directly).
- Styling uses Tailwind v4 + DaisyUI via CSS plugin directives in `frontend/src/assets/main.css`.
- Sidebar/menu behavior relies on DaisyUI drawer classes in `frontend/src/components/navbar/NavBar.vue`.
- Backend app code is concentrated in Django app `web`; add new routes in `backend/web/urls.py` and views under `backend/web/views/`.

## Integration Gotchas
- `backend/web/templates/index.html` hardcodes hashed Vite asset filenames (for example `index-fDZVQzLh.js`); after `npm run build`, update this template if hashes change.
- Django `STATICFILES_DIRS` expects `backend/static` in development (`backend/backend/settings.py`), so missing build output can break page rendering.
- API + template are served from Django root path; avoid introducing frontend router history routes without aligning Django URL handling.

## Quick File Map
- Backend config: `backend/backend/settings.py`, `backend/backend/urls.py`
- Backend web app: `backend/web/urls.py`, `backend/web/views/index.py`, `backend/web/templates/index.html`
- Frontend entry points: `frontend/src/main.js`, `frontend/src/App.vue`, `frontend/src/router/index.js`
- Frontend build config: `frontend/vite.config.js`, `frontend/package.json`

