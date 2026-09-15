# Overview del producto

## Alcance verificable

El repositorio implementa un Financial Metrics Dashboard con frontend React + TypeScript y backend FastAPI, segun `README.md`. El frontend carga movimientos desde `GET /api/metrics`, calcula KPIs y datos mensuales, y presenta tarjetas y dos graficos.

Evidencia:

- `README.md`: describe el dashboard y la arquitectura React + TypeScript/FastAPI.
- `frontend/src/App.tsx`: solicita `/api/metrics`, invoca `computeKPIs` y `computeMonthlyData`, y monta `KPIRow`, `IncomeOutcomeChart` y `ProfitPercentChart`.
- `backend/app/routes.py`: implementa `/api/metrics`, filtros, resumen, categorias principales, comparacion, alertas y segmentos B2B/B2C.

## Flujo observable

```text
Navegador -> frontend:5173 -> proxy Vite /api -> backend:8000 -> movimientos mock -> KPIs y graficos en cliente
```

Evidencia: `frontend/vite.config.ts` dirige `/api` a `http://backend:8000`; `docker-compose.yml` define ambos servicios y publica los puertos 5173 y 8000.

## Limites confirmados

No hay persistencia configurada: cada endpoint genera movimientos con `generate_mock_movements(seed=42)`. No describir estos datos como transacciones reales ni como una integracion con base de datos.

Evidencia: `backend/app/routes.py` genera la lista de movimientos dentro de cada endpoint y no hay dependencias de base de datos en `backend/requirements.txt`.