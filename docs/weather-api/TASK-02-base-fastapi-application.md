# TASK-02 — Aplicación FastAPI base

Status: Planned

Prerequisites:

- [TASK-01 — Inicialización Python](TASK-01-python-project-initialization.md)

Next:

- [TASK-03 — Configuración y lifecycle](TASK-03-configuration-and-lifecycle.md)

## Contexto

El proyecto Python existe, pero no tiene aplicación ASGI. Este incremento introduce únicamente FastAPI, ejecución local y liveness.

## Objetivo

Crear una aplicación FastAPI mínima con `GET /health/live` y pruebas HTTP.

## Alcance

- Agregar FastAPI, servidor ASGI y dependencias de test estrictamente necesarias.
- Crear una factoría o instancia de aplicación simple y comprobable.
- Implementar `/health/live` sin dependencias.
- Definir un comando de ejecución local.
- Probar status, JSON y método permitido.

## Fuera de alcance

- Readiness, configuración completa y lifespan de proveedores.
- Contrato final de errores y middleware.
- Integración meteorológica.
- CORS y autenticación.

## Archivos probablemente afectados

- `pyproject.toml` y lockfile.
- Paquete de aplicación mínimo.
- Tests HTTP.

## Pasos esperados

1. Incorporar dependencias con `uv`.
2. Crear la app sin routers o capas innecesarias.
3. Añadir liveness.
4. Probar mediante el cliente recomendado por FastAPI.
5. Documentar el comando local mínimo.

## Restricciones

- No crear un árbol completo desde el inicio.
- `live` no consulta configuración, caché ni red.
- No incorporar librerías de logging o DI.

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

- La app inicia con el servidor documentado.
- `/health/live` responde `200` sin dependencias.
- Las pruebas no abren un puerto real.
- Dependencias y lockfile están sincronizados.
- La estructura continúa siendo mínima.

## Resultado esperado

Aplicación FastAPI base lista para configuración y lifecycle.

## Siguiente tarea

Detenerse e informar resultados. Continuar con [TASK-03](TASK-03-configuration-and-lifecycle.md).
