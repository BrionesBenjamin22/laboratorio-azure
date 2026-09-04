# TASK-03 — Configuración y lifecycle

Status: Planned

Prerequisites:

- [TASK-02](TASK-02-base-fastapi-application.md)

Next:

- [TASK-04](TASK-04-http-contract-and-middleware.md)

## Contexto y objetivo

Agregar configuración validada, logging base y lifespan antes de inicializar recursos.

## Alcance

- Configurar ambiente, red y logging desde variables.
- Crear `.env.example` seguro.
- Definir lifespan para recursos futuros y cierre ordenado.
- Mantener readiness pendiente hasta disponer de PostgreSQL.

## Fuera de alcance

- Conexión a base, JWT y middleware HTTP.

## Archivos probablemente afectados

- Configuración, arranque, `.env.example` y tests.

## Pasos esperados

Definir esquema mínimo, validar al inicio, configurar logging estándar y probar lifespan.

## Restricciones

- No cargar secretos implícitamente ni duplicar señales del servidor ASGI.

## Tests y validaciones

Ejecutar suite y calidad; probar configuración válida, inválida, startup y shutdown.

## Criterios de aceptación

- Fallo temprano seguro, logging sin secretos y recursos cerrados por lifespan.

## Resultado esperado

Aplicación configurable. Detenerse antes de TASK-04.
