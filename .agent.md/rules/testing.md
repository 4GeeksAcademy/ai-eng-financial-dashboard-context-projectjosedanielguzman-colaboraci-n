# Testing y cambios compartidos

## Alcance

Cambios en endpoints, filtros, transformaciones financieras y sus contratos.

## Justificacion

Las pruebas backend cubren los filtros y rutas de segmentacion. Las pruebas frontend ejercen calculos puros con datos pequenos, desordenados y entre anos.

## Guia del proyecto

1. Cada endpoint o filtro nuevo debe incluir una prueba de respuesta valida y una prueba de la condicion que lo distingue de la ruta base.
   - Hecho: `backend/tests/test_routes.py` prueba fechas, categorias, tipo de operacion y que B2B/B2C solo devuelven su segmento.
   - Validacion: ejecuta `cd backend && pytest -q`; añade una asercion de pertenencia al segmento, categoria o rango incorporado.

2. Los calculos financieros del cliente deben probarse con conjuntos pequenos que incluyan orden cronologico, cambio de ano y ausencia de ingresos cuando esas condiciones sean pertinentes.
   - Hecho: `frontend/src/lib/financial-utils.test.ts` cubre datos desordenados de diciembre de 2025 y enero de 2026, y un conjunto sin ingresos.
   - Validacion: ejecuta `cd frontend && npm test`; para un calculo nuevo, agrega un caso con resultado numerico exacto.

3. Un cambio en `/api/metrics` o `FinancialMovement` requiere las pruebas de backend y frontend antes de confirmarse.
   - Hecho: el backend devuelve `list[FinancialMovement]` y `App.tsx` entrega esa lista a utilidades de calculo locales.
   - Validacion: ejecuta `cd backend && pytest` y `cd frontend && npm test && npm run build`.