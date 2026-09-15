# Estado actual

## Funciona y fue validado

- Las pruebas backend pasaron: 15 pruebas mediante `docker compose run --rm backend pytest -q`.
- Las pruebas frontend pasaron: 5 pruebas mediante `docker compose run --rm frontend npm test`.
- El build frontend completó mediante `docker compose run --rm frontend npm run build`.
- `docker compose config` resolvio los servicios `frontend` y `backend`.

Evidencia: `.agent.md/rules/validation.md` registra esas ejecuciones y sus resultados.

## Gaps observados

- El encabezado muestra `2024 - Full Year`, pero el backend deriva las fechas de `date.today()`.
- El frontend consume `/api/metrics`; los endpoints de resumen, alertas, comparacion, categorias y segmentos no se invocan desde `App.tsx`.
- CORS permite `*` con credenciales habilitadas.
- Los Dockerfiles son de desarrollo: usan Vite dev server y Debugpy/Uvicorn con `--reload`.
- El build frontend advierte un chunk de 584 kB.

Evidencia: `frontend/src/App.tsx`, `frontend/src/components/dashboard/dashboard-header.tsx`, `backend/app/routes.py`, `backend/app/main.py`, `frontend/Dockerfile`, `backend/Dockerfile` y `.agent.md/rules/validation.md`.

## Prioridades basadas en evidencia

1. Al cambiar el periodo o el contrato de datos, alinear el texto visible con el rango de movimientos y actualizar pruebas de backend y frontend.
2. Antes de desplegar fuera de desarrollo, separar la configuracion de produccion y restringir CORS por entorno.
3. Si la interfaz incorpora endpoints adicionales existentes, definir una fuente de agregacion por vista para no mezclar calculos locales y respuestas agregadas.

Estas prioridades no representan una hoja de ruta de producto: se derivan de los gaps documentados arriba.