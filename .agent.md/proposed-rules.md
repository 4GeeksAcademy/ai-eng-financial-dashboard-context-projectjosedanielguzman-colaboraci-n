# Reglas propuestas para contribuidores y agentes

Fecha: 2026-09-15

## Nota de analisis

Estas reglas se basan en el comportamiento y la estructura actuales. No cambian la aplicacion. La carpeta `.agent.md` se usa por solicitud de este cambio; `AGENTS.md` declara `.agents/rules` y `.agents/skills` como ubicaciones esperadas para instrucciones y skills.

## Arquitectura

### Una fuente de agregacion por vista

**Regla:** No combinar calculos locales de movimientos con respuestas agregadas del backend en una misma vista sin declarar una fuente de verdad.

**Hecho del repo:** `App.tsx` obtiene `/api/metrics` y calcula KPIs y series mensuales mediante `computeKPIs` y `computeMonthlyData`, mientras `routes.py` tambien expone `/api/metrics/summary`.

**Referencias:** [frontend/src/App.tsx](../frontend/src/App.tsx#L15-L37), [frontend/src/lib/financial-utils.ts](../frontend/src/lib/financial-utils.ts#L21-L65), [backend/app/routes.py](../backend/app/routes.py#L267-L286).

### Separar logica de negocio de rutas al crecer

**Regla:** Extraer generacion, filtrado, agregacion y alertas de `routes.py` a un modulo de servicio cuando una modificacion afecte mas de una de esas responsabilidades.

**Hecho del repo:** un unico archivo contiene los modelos, la generacion mock, los filtros, los calculos de resumen y las definiciones de endpoints.

**Referencias:** [backend/app/routes.py](../backend/app/routes.py#L20-L226), [backend/app/routes.py](../backend/app/routes.py#L229-L365).

### Hacer reproducible el tiempo de los mocks

**Regla:** Las funciones de datos mock usadas en pruebas deben recibir una fecha o reloj explicito; no depender directamente de `date.today()` cuando el resultado sea parte de un contrato verificable.

**Hecho del repo:** `generate_mock_movements(seed=42)` fija la aleatoriedad, pero toma la fecha actual y `_year_for_month` decide el ano de cada movimiento a partir de ella.

**Referencias:** [backend/app/routes.py](../backend/app/routes.py#L43-L82).

## Contratos y naming

### Derivar el periodo visible de los datos

**Regla:** El texto del periodo del dashboard debe proceder del rango recibido o de una configuracion comun; no debe quedar fijo en el componente de pantalla.

**Hecho del repo:** `App.tsx` pasa `2024 - Full Year` al encabezado, y el backend genera fechas en funcion de `date.today()`.

**Referencias:** [frontend/src/App.tsx](../frontend/src/App.tsx#L43-L45), [frontend/src/components/dashboard/dashboard-header.tsx](../frontend/src/components/dashboard/dashboard-header.tsx#L7-L7), [backend/app/routes.py](../backend/app/routes.py#L43-L45).

### Mantener contratos duplicados alineados

**Regla:** Todo cambio en campos o valores permitidos de una respuesta FastAPI debe actualizar los modelos TypeScript equivalentes y sus pruebas.

**Hecho del repo:** los valores de operacion, categoria y tipo de negocio estan declarados como `Literal` en backend y como uniones TypeScript en frontend.

**Referencias:** [backend/app/routes.py](../backend/app/routes.py#L11-L39), [frontend/src/lib/financial-types.ts](../frontend/src/lib/financial-types.ts#L1-L26).

### Conservar una convencion de rutas explicita

**Regla:** Las rutas nuevas deben seguir la convencion elegida para sus propositos y documentar excepciones, como salud fuera de `/api`.

**Hecho del repo:** existe `GET /health`, mientras los endpoints de dominio usan el prefijo `/api/metrics`.

**Referencias:** [backend/app/routes.py](../backend/app/routes.py#L229-L230), [backend/app/routes.py](../backend/app/routes.py#L233-L365).

## Testing

### Probar endpoint y regla introducida

**Regla:** Cada endpoint o filtro nuevo debe cubrir una respuesta valida y la regla que lo diferencia de la ruta base.

**Hecho del repo:** las pruebas verifican filtros de fecha, categoria y operacion, y tambien que las rutas B2B y B2C solo devuelven el segmento correspondiente.

**Referencias:** [backend/tests/test_routes.py](../backend/tests/test_routes.py#L29-L100).

### Aislar calculos financieros puros

**Regla:** Los calculos del frontend deben conservar pruebas con conjuntos pequenos que cubran orden cronologico, cambio de ano y ausencia de ingresos.

**Hecho del repo:** `financial-utils.test.ts` cubre movimientos sin ingresos y datos desordenados entre diciembre de 2025 y enero de 2026.

**Referencias:** [frontend/src/lib/financial-utils.test.ts](../frontend/src/lib/financial-utils.test.ts#L35-L96).

### Validar ambos lados del contrato de movimientos

**Regla:** Al cambiar `/api/metrics` o `FinancialMovement`, ejecutar pruebas del backend y del frontend, ya que el cliente calcula las metricas desde la respuesta cruda.

**Hecho del repo:** el backend retorna `list[FinancialMovement]`; el frontend recibe esa lista y la transforma con utilidades propias.

**Referencias:** [backend/app/routes.py](../backend/app/routes.py#L233-L246), [frontend/src/App.tsx](../frontend/src/App.tsx#L15-L37), [frontend/src/lib/financial-utils.test.ts](../frontend/src/lib/financial-utils.test.ts#L17-L83).

## Documentacion y DX

### Distinguir Docker de ejecucion local

**Regla:** Toda instruccion de ejecucion debe indicar si aplica a Docker o al host, y especificar el origen del backend cuando no exista el hostname de Compose.

**Hecho del repo:** Vite redirige `/api` a `http://backend:8000`, y Compose define un servicio llamado `backend` que publica el puerto 8000.

**Referencias:** [frontend/vite.config.ts](../frontend/vite.config.ts#L9-L15), [docker-compose.yml](../docker-compose.yml#L1-L19), [README.md](../README.md#L32-L43).

### Usar la ruta declarada para reglas de agentes

**Regla:** Al crear reglas ejecutables por agentes, ubicarlas bajo `.agents/rules`; los documentos de apoyo que vivan en `.agent.md` deben enlazarse o mencionarse desde esa estructura.

**Hecho del repo:** `AGENTS.md` solicita revisar `.agents/rules`, `.agents/skills` y, si existe, `memory-bank`; el documento actual esta en `.agent.md` por requisito de este cambio.

**Referencias:** [AGENTS.md](../AGENTS.md#L3-L12), [proposed-rules.md](proposed-rules.md#L1-L7).

### Registrar verificacion ejecutada y pendiente por separado

**Regla:** Los rastros de verificacion deben diferenciar comandos realmente ejecutados de comandos solo recomendados.

**Hecho del repo:** `verification.md` marca inspecciones como realizadas y enumera `pytest`, `npm run lint`, `npm test` y `npm run build` como comandos recomendados.

**Referencias:** [verification.md](verification.md#L11-L29).

### Mantener el idioma de producto coherente

**Regla:** Una modificacion de copy debe mantener el idioma elegido en etiquetas, estados de error y formatos regionales del dashboard.

**Hecho del repo:** el error de carga esta en espanol; las tarjetas estan en ingles y el formato monetario usa la configuracion `en-US` con USD.

**Referencias:** [frontend/src/App.tsx](../frontend/src/App.tsx#L31-L35), [frontend/src/components/dashboard/kpi-row.tsx](../frontend/src/components/dashboard/kpi-row.tsx#L13-L42), [frontend/src/lib/financial-utils.ts](../frontend/src/lib/financial-utils.ts#L69-L84).

## Configuracion y seguridad

### Restringir CORS por entorno

**Regla:** En despliegues que no sean locales, declarar origenes permitidos concretos y no usar `*` con credenciales habilitadas.

**Hecho del repo:** FastAPI configura `allow_origins=["*"]` y `allow_credentials=True`.

**Referencias:** [backend/app/main.py](../backend/app/main.py#L6-L13).

### No reutilizar imagenes de desarrollo como produccion

**Regla:** Mantener una configuracion de produccion separada cuando se despliegue; no usar sin revision el servidor Vite de desarrollo ni Uvicorn con recarga y Debugpy.

**Hecho del repo:** el contenedor frontend ejecuta `npm run dev`, y el backend inicia Debugpy y Uvicorn con `--reload`.

**Referencias:** [frontend/Dockerfile](../frontend/Dockerfile#L11-L13), [backend/Dockerfile](../backend/Dockerfile#L11-L13).
