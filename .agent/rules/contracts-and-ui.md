# Contratos, periodo y copy

## Alcance

Cambios en modelos FastAPI, tipos TypeScript y componentes del dashboard.

## Justificacion

El contrato `FinancialMovement` esta duplicado entre Pydantic y TypeScript. El backend genera fechas relativas, pero el encabezado recibe un periodo fijo.

## Guia del proyecto

1. Todo cambio en `FinancialMovement`, categorias, operaciones o segmentos debe actualizar en el mismo cambio el modelo Pydantic, el tipo TypeScript y las pruebas afectadas.
   - Hecho: `backend/app/routes.py` usa `Literal` para esos valores y `frontend/src/lib/financial-types.ts` mantiene las uniones equivalentes.
   - Validacion: ejecuta `cd backend && pytest` y `cd frontend && npm test` despues de modificar el contrato.

2. El periodo visible del dashboard debe derivarse del rango recibido o de una configuracion compartida, no de un literal en la pantalla.
   - Hecho: `App.tsx` pasa `2024 - Full Year`, mientras `generate_mock_movements` calcula fechas desde `date.today()`.
   - Validacion: modifica un conjunto de datos de prueba para cruzar de 2025 a 2026 y comprueba que el texto de periodo resultante cambia con el rango.

3. El copy nuevo debe usar el idioma y formato regional seleccionados para toda la vista.
   - Hecho: el error de `App.tsx` esta en espanol, `kpi-row.tsx` usa etiquetas inglesas y `financial-utils.ts` formatea moneda como `en-US`/USD.
   - Validacion: antes de confirmar, busca el texto nuevo en la vista y revisa que sus etiquetas, estados vacios y formateadores pertenezcan a la misma eleccion de idioma.