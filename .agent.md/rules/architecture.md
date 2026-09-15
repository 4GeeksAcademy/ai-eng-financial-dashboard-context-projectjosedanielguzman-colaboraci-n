# Arquitectura de datos y API

## Alcance

Cambios en `backend/app`, contratos de metricas y vistas que los consumen.

## Justificacion

`backend/app/routes.py` genera, filtra y agrega movimientos; `frontend/src/App.tsx` consume movimientos crudos y calcula sus propios KPIs y series mensuales. Las decisiones de datos afectan ambos lados.

## Guia del proyecto

1. Para una vista, declara una sola fuente de agregacion: movimientos crudos del endpoint `/api/metrics` o una respuesta agregada como `/api/metrics/summary`. No combines ambas sin definir cual prevalece.
   - Hecho: `App.tsx` solicita `/api/metrics` y usa `computeKPIs` y `computeMonthlyData`; `routes.py` tambien expone `/api/metrics/summary`.
   - Validacion: al cambiar una vista, comprueba que sus solicitudes `fetch` y sus transformaciones no consuman ambas fuentes para el mismo KPI.

2. Cuando una modificacion de dominio abarque generacion, filtrado y agregacion, mueve esas funciones fuera de `backend/app/routes.py` a un modulo de servicio y deja la ruta como adaptador HTTP.
   - Hecho: `routes.py` contiene modelos Pydantic, `generate_mock_movements`, filtros, agregaciones, alertas y endpoints.
   - Validacion: ejecuta `cd backend && pytest` tras extraer o modificar logica; las pruebas importan utilidades y ejercen endpoints de ese modulo.

3. Los mocks que participen en pruebas deben aceptar una fecha o reloj explicito; no introduzcas nuevas dependencias directas de `date.today()` en funciones que deban ser reproducibles.
   - Hecho: `generate_mock_movements(seed=42)` fija el azar, pero usa `date.today()` y `_year_for_month` para asignar anos.
   - Validacion: prueba la funcion con la misma semilla y una fecha inyectada, comprobando que devuelve exactamente las mismas fechas en dos ejecuciones.

4. Las rutas de dominio deben conservar el prefijo `/api`; una excepcion operativa, como `/health`, debe documentarse junto a su definicion o en el README.
   - Hecho: existe `GET /health`, mientras las rutas de metricas usan `/api/metrics`.
   - Validacion: ejecuta `cd backend && pytest -q` despues de agregar una ruta y confirma que su prueba llama a la URL acordada.