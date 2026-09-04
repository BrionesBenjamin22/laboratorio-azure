# TASK-16 — Detalle de nota

Status: Planned

Prerequisites:

- [TASK-15](TASK-15-list-notes.md)

Next:

- [TASK-17](TASK-17-update-note.md)

## Contexto y objetivo

Implementar `GET /api/v1/notes/{note_id}` preservando privacidad por propietario.

## Alcance

- Validar UUID.
- Buscar nota activa por ID y propietario en una sola operación segura.
- Devolver contrato de detalle con versión.
- Responder como no encontrado para nota inexistente, eliminada o ajena.

## Fuera de alcance

- Edición, historial y acceso administrativo.

## Archivos probablemente afectados

- Repositorio, servicio, ruta, schema y tests.

## Pasos esperados

Implementar query con ownership, mapear ausencia uniformemente y probar aislamiento.

## Restricciones

- No consultar primero por ID y autorizar después si eso filtra existencia.

## Tests y validaciones

Cubrir propia, ajena, eliminada, inexistente, UUID inválido y usuario inactivo.

## Criterios de aceptación

- Solo el propietario obtiene el detalle y los demás casos son indistinguibles públicamente.

## Resultado esperado

Detalle privado. Detenerse antes de TASK-17.
