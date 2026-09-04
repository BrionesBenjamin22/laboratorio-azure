# TASK-18 — Eliminar nota

Status: Planned

Prerequisites:

- [TASK-17](TASK-17-update-note.md)

Next:

- [TASK-19](TASK-19-note-history.md)

## Contexto y objetivo

Implementar `DELETE /api/v1/notes/{note_id}` mediante soft delete e historial.

## Alcance

- Validar propietario y nota activa.
- Establecer `deleted_at`, incrementar versión y registrar evento en una transacción.
- Definir respuesta idempotente sin filtrar existencia ajena.
- Excluir inmediatamente la nota de listados y detalle.

## Fuera de alcance

- Borrado físico, restauración y purga automática.

## Archivos probablemente afectados

- Servicio, repositorio, historial, ruta y tests.

## Pasos esperados

Definir semántica idempotente, implementar actualización condicional y probar visibilidad posterior.

## Restricciones

- No eliminar físicamente ni perder historial.

## Tests y validaciones

Cubrir éxito, repetición, ajena, inexistente, rollback e invisibilidad posterior.

## Criterios de aceptación

- Soft delete e historial son atómicos y la repetición no revela información.

## Resultado esperado

Eliminación lógica completa. Detenerse antes de TASK-19.
