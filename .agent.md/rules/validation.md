# Notas de analisis y validacion

## Analisis

Las reglas se han separado por el limite de cambio que controlan: API y datos, contrato de UI, pruebas, y operacion. Cada regla contiene un hecho verificable del repositorio y una validacion concreta; no sustituye las pruebas funcionales de cambios futuros.

La ubicacion `.agent.md/rules` agrupa las reglas con las notas de agentes y `AGENTS.md` se actualizo para que futuros agentes descubran esta ruta. Cualquier automatizacion externa que solo lea la ruta historica `.agents/rules` debe actualizarse antes de depender de estas reglas.

## Tareas ejecutadas por regla

| Regla | Tarea real | Resultado |
| --- | --- | --- |
| Fuente de agregacion | Revision de `App.tsx`, utilidades y ruta de resumen. | Superada: el dashboard usa movimientos crudos y calculos locales; no mezcla la respuesta de resumen. |
| Servicio al crecer | `docker compose run --rm backend pytest -q`. | Superada: 15 pruebas verifican los endpoints y utilidades hoy concentrados en `routes.py`. |
| Tiempo reproducible | Revision de `generate_mock_movements` y sus pruebas. | Confirmada la limitacion: el seed es fijo, pero las fechas usan `date.today()`; la regla conserva una accion correctiva concreta. |
| Convencion de rutas | `docker compose run --rm backend pytest -q`. | Superada: las pruebas llaman a `/health` y rutas bajo `/api/metrics` de forma explicita. |
| Contratos duplicados | `docker compose run --rm backend pytest -q` y `docker compose run --rm frontend npm test`. | Superada: 15 pruebas backend y 5 frontend pasan sobre el contrato actual. |
| Periodo visible | Revision de `App.tsx` y `routes.py`. | Confirmado: el encabezado sigue fijo en `2024 - Full Year` frente a fechas dinamicas; la regla queda como requisito para el siguiente ajuste de UI. |
| Copy regional | Revision de `App.tsx`, `kpi-row.tsx` y `financial-utils.ts`. | Confirmada la mezcla actual de espanol e ingles; la regla exige revisar este conjunto al modificar copy. |
| Pruebas de endpoints | `docker compose run --rm backend pytest -q`. | Superada: 15 pruebas cubren health, filtros, segmentos, resumen, categorias, comparacion y alertas. |
| Calculos puros | `docker compose run --rm frontend npm test`. | Superada: 5 pruebas cubren KPIs, falta de ingresos, orden entre anos y formateadores. |
| Cambio compartido | `docker compose run --rm frontend npm run build`. | Superada: TypeScript y el build de Vite completan correctamente. |
| Docker frente a host | `docker compose config`. | Superada: Compose define `backend` y `frontend`, compatibles con el destino del proxy. |
| Imagen de produccion | Revision de ambos Dockerfiles. | Confirmado: usan Vite dev server, Debugpy y `--reload`; la regla evita emplearlos como configuracion de produccion. |
| CORS por entorno | Revision de `backend/app/main.py`. | Confirmado: CORS permite `*` con credenciales; la regla conserva el cambio requerido antes de exponer el servicio. |
| Descubrimiento de reglas | Actualizacion de `AGENTS.md` y `find .agent.md/rules -type f`. | Superada: `AGENTS.md` declara `.agent.md/rules` y los cinco documentos son localizables. |
| Higiene de commits | `git diff --check`. | Superada: sin errores de espacio antes de confirmar. |

Las dependencias no estan instaladas en el host (`fastapi` y `vitest` faltaban), pero las validaciones de aplicacion se completaron en Docker Compose, que es el entorno de ejecucion documentado. El build frontend emitio la advertencia existente de un chunk de 584 kB; no bloquea esta documentacion, pero debe revisarse si crece el bundle.

## Criterio de refinamiento

Una regla debe conservarse solo si contiene los cuatro elementos: alcance, justificacion basada en un archivo concreto, una instruccion especifica y una validacion que un contribuidor pueda ejecutar o revisar.