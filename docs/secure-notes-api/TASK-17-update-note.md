# TASK-17 — Actualizar nota

Status: Planned

Prerequisites:

- [TASK-16](TASK-16-note-detail.md)

Next:

- [TASK-18](TASK-18-delete-note.md)

## Contexto y objetivo

Implementar `PATCH /api/v1/notes/{note_id}` con diferencias reales y concurrencia optimista.

## Alcance

- Aceptar título, contenido y versión esperada.
- Comparar valores normalizados y escribir solo diferencias.
- No ejecutar UPDATE ni historial ante no-op.
- Incrementar versión y registrar diferencias seguras atómicamente.
- Responder `409` ante versión obsoleta.

## Fuera de alcance

- Cambiar propietario, restaurar, compartir o actualizar notas eliminadas.

## Archivos probablemente afectados

- Schemas, servicio, repositorio, historial, ruta y tests.

## Pasos esperados

Validar ownership/versión, calcular diff permitido y persistir con condición de versión.

## Restricciones

- No aceptar campos administrados ni guardar snapshots sensibles innecesarios.

## Tests y validaciones

Cubrir cambio parcial, no-op, conflicto, rollback, nota ajena/eliminada y concurrencia.

## Criterios de aceptación

- Solo cambios reales incrementan versión y generan un único historial atómico.

## Resultado esperado

Edición segura. Detenerse antes de TASK-18.
