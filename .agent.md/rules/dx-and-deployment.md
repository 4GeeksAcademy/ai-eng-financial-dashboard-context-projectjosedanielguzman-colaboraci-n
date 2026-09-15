# Desarrollo, despliegue y trazabilidad

## Alcance

Docker Compose, Vite, CORS, documentos de agentes y commits de mantenimiento.

## Justificacion

El entorno Compose resuelve el hostname `backend`, los Dockerfiles inician herramientas de desarrollo y `AGENTS.md` declara una ruta distinta para reglas de agentes.

## Guia del proyecto

1. Las instrucciones de ejecucion deben distinguir Docker del host y especificar el origen de API necesario en cada caso.
   - Hecho: `vite.config.ts` dirige `/api` a `http://backend:8000`; `docker-compose.yml` define el servicio `backend` y README documenta `docker compose up --build`.
   - Validacion: ejecuta `docker compose config` tras modificar Compose o Vite y revisa que el servicio y el destino del proxy sigan coincidiendo.

2. No reutilices sin revision los Dockerfiles actuales para produccion; crea una configuracion separada si se elimina el modo de desarrollo.
   - Hecho: `frontend/Dockerfile` ejecuta `npm run dev`; `backend/Dockerfile` inicia Debugpy y Uvicorn con `--reload`.
   - Validacion: la configuracion de produccion debe construir sin `--reload`, Debugpy ni el servidor Vite de desarrollo.

3. Restringe CORS por entorno antes de exponer el backend fuera de desarrollo local.
   - Hecho: `backend/app/main.py` usa `allow_origins=["*"]` junto con `allow_credentials=True`.
   - Validacion: una prueba de configuracion debe comprobar los origenes permitidos para el entorno de despliegue.

4. Los documentos de trabajo de agentes deben vivir en `.agent.md/rules` para este repositorio y declarar la diferencia respecto a la ruta historica de `AGENTS.md`.
   - Hecho: `AGENTS.md` menciona `.agent.md/rules`, mientras la convencion anterior usaba `.agents/rules`.
   - Validacion: `find .agent.md/rules -type f` debe listar las reglas y cada archivo debe contener alcance, justificacion, guia y hechos del repositorio.

5. Todo commit de documentacion o mantenimiento debe registrar que validaciones se ejecutaron y cuales solo se recomiendan.
   - Hecho: `.agent.md/verification.md` marca inspecciones realizadas y deja `pytest`, lint, test y build como comandos recomendados.
   - Validacion: antes de confirmar, ejecuta `git diff --check` y deja el resultado real en la descripcion del commit, nota de PR o rastro de verificacion.