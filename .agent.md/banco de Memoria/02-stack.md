# Stack tecnologico

## Aplicacion

- Frontend: TypeScript, React, React DOM, Vite y Tailwind CSS.
- Visualizacion: Recharts; iconos: Lucide React.
- Backend: Python, FastAPI, Uvicorn y Pydantic incluido por FastAPI.

Evidencia: `frontend/package.json` declara React, Vite, Tailwind, Recharts y Lucide React; `backend/requirements.txt` declara FastAPI y `uvicorn[standard]`.

## Pruebas y calidad

- Frontend: Vitest, ESLint y TypeScript mediante los scripts `test`, `lint` y `build`.
- Backend: Pytest, pytest-cov y TestClient de FastAPI.

Evidencia: `frontend/package.json`, `frontend/src/lib/financial-utils.test.ts`, `backend/requirements.txt` y `backend/tests/test_routes.py`.

## Infraestructura y tooling

- Docker Compose ejecuta los servicios `frontend` y `backend`.
- El frontend se publica en 5173; backend en 8000; Debugpy se expone en 5678.
- El contenedor frontend ejecuta Vite en desarrollo; el backend ejecuta Debugpy y Uvicorn con recarga.

Evidencia: `docker-compose.yml`, `frontend/Dockerfile` y `backend/Dockerfile`.