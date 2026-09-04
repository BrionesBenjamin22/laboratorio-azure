# TASK-14 — Crear nota

Status: Planned

Prerequisites:

- [TASK-13](TASK-13-note-persistence.md)

Next:

- [TASK-15](TASK-15-list-notes.md)

## Contexto y objetivo

Implementar `POST /api/v1/notes` como primera operación del agregado nota.

## Alcance

- Validar límites de título y contenido.
- Asignar propietario desde autenticación.
- Rechazar campos administrados enviados por cliente.
- Crear nota y evento de creación en una transacción.
- Responder `201` con mensaje de éxito y datos propios.

## Fuera de alcance

- Listado, edición, eliminación y notas compartidas.

## Archivos probablemente afectados

- Schemas, servicio, ruta, repositorios y tests.

## Pasos esperados

Definir contrato, validar, persistir atómicamente y mapear errores seguros.

## Restricciones

- No aceptar `owner_id`, versión, timestamps o estado desde el payload.

## Tests y validaciones

Cubrir éxito, límites, usuario inactivo, campos prohibidos y rollback del historial.

## Criterios de aceptación

- Nota e historial se crean atómicamente para el usuario autenticado.

## Resultado esperado

Alta de notas completa. Detenerse antes de TASK-15.
