# TASK-01 — Inicialización del proyecto Python

Status: Planned

Prerequisites:

- Bootstrap del repositorio completado.

Next:

- [TASK-02](TASK-02-base-fastapi-application.md)

## Contexto y objetivo

Crear una base Python aislada y reproducible antes de incorporar FastAPI.

## Alcance

- Verificar Python y `uv`; instalar `uv` solo con autorización.
- Crear `pyproject.toml`, lockfile, paquete fuente y tests mínimos.
- Definir versión Python y herramientas de test, formato y análisis.

## Fuera de alcance

- FastAPI, PostgreSQL, JWT, endpoints y código funcional.

## Archivos probablemente afectados

- `apps/secure-notes-api/pyproject.toml`, `uv.lock` y estructura mínima.

## Pasos esperados

Inspeccionar herramientas, inicializar dentro del módulo, sincronizar un entorno local aislado y verificar que no se instalaron paquetes globalmente.

## Restricciones

- No mezclar administradores ni crear capas anticipadas.

## Tests y validaciones

Ejecutar `uv lock --check`, `uv run python --version` y los comandos de calidad configurados.

## Criterios de aceptación

- Proyecto y lockfile propios, entorno ignorado y ninguna dependencia funcional prematura.

## Resultado esperado

Base Python reproducible. Detenerse antes de TASK-02.
