# TASK-03 — Configuración y lifecycle

Status: Planned

Prerequisites:

- [TASK-02 — Aplicación FastAPI base](TASK-02-base-fastapi-application.md)

Next:

- [TASK-04 — Contrato y middleware HTTP](TASK-04-http-contract-and-middleware.md)

## Contexto

La app necesita configuración validada, readiness y un lifecycle explícito antes de incorporar recursos externos.

## Objetivo

Definir configuración tipada, logging base, lifespan y `/health/ready` sin consultar al proveedor.

## Alcance

- Leer y validar host, puerto, ambiente, log level y parámetros internos existentes.
- Introducir una dependencia de configuración solo si aporta validación concreta.
- Configurar logging estándar con campos consistentes.
- Implementar lifespan para inicializar y cerrar recursos futuros.
- Implementar readiness basado en configuración e inicialización internas.
- Documentar graceful shutdown bajo el servidor ASGI.
- Crear `.env.example` sin secretos.

## Fuera de alcance

- Configuración del proveedor aún no seleccionado.
- Cliente HTTP externo.
- Middleware por request.
- CORS, rate limiting y Kubernetes.

## Archivos probablemente afectados

- Configuración y arranque de aplicación.
- Handlers de health.
- `.env.example`.
- Tests de configuración y lifespan.

## Pasos esperados

1. Definir el mínimo esquema de configuración.
2. Validar antes de aceptar tráfico.
3. Incorporar lifespan sin recursos ficticios.
4. Exponer readiness interno.
5. Probar configuración válida e inválida y cierre.

## Restricciones

- Readiness no realiza llamadas externas.
- No cargar `.env` implícitamente en producción.
- No registrar valores sensibles.
- No duplicar el manejo de señales propio del servidor ASGI.

## Validaciones

```powershell
Set-Location apps/weather-api
uv run pytest
uv run ruff check .
uv run ruff format --check .
```

```bash
cd apps/weather-api
uv run pytest
uv run ruff check .
uv run ruff format --check .
```

## Criterios de aceptación

- La configuración inválida impide un inicio ambiguo.
- `live` y `ready` conservan semánticas diferentes.
- `ready` no depende del proveedor meteorológico.
- Lifespan libera recursos registrados.
- Los tests cubren inicio y cierre.

## Resultado esperado

Aplicación configurable y preparada para administrar recursos asíncronos.

## Siguiente tarea

Detenerse e informar resultados. Continuar con [TASK-04](TASK-04-http-contract-and-middleware.md).
