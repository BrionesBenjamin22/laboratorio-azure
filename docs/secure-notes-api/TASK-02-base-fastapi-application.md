# TASK-02 — Aplicación FastAPI base

Status: Planned

Prerequisites:

- [TASK-01](TASK-01-python-project-initialization.md)

Next:

- [TASK-03](TASK-03-configuration-and-lifecycle.md)

## Contexto y objetivo

Incorporar la mínima aplicación ASGI y `GET /health/live`.

## Alcance

- Agregar FastAPI, servidor ASGI y dependencias de test necesarias.
- Crear app comprobable y liveness sin dependencias.
- Documentar ejecución local mínima.

## Fuera de alcance

- Configuración completa, readiness, base de datos y autenticación.

## Archivos probablemente afectados

- Dependencias, paquete de aplicación y tests HTTP.

## Pasos esperados

Agregar dependencias con `uv`, crear la app mínima, implementar live y probarla sin puerto real.

## Restricciones

- No crear routers o servicios vacíos; live no consulta nada externo.

## Tests y validaciones

Ejecutar `uv lock --check`, `uv run pytest`, `uv run ruff check .` y `uv run ruff format --check .`.

## Criterios de aceptación

- La app inicia, live responde `200` y las pruebas son aisladas.

## Resultado esperado

Aplicación base. Detenerse antes de TASK-03.
