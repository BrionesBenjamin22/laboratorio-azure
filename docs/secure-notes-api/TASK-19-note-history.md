# TASK-19 — Historial de nota

Status: Planned

Prerequisites:

- [TASK-18](TASK-18-delete-note.md)

Next:

- [TASK-20](TASK-20-authentication-hardening.md)

## Contexto y objetivo

Exponer `GET /api/v1/notes/{note_id}/history?page=1` para el propietario.

## Alcance

- Autorizar historial de notas propias, incluidas eliminadas según política documentada.
- Paginar en PostgreSQL con 3 eventos por página y orden determinista descendente.
- Devolver tipo, campos cambiados, versión, actor, fecha y request ID seguros.
- Probar eventos de creación, actualización real y eliminación.

## Fuera de alcance

- Historial global, acceso admin, restauración y contenido completo de snapshots.

## Archivos probablemente afectados

- Repositorio de historial, servicio, ruta, schemas y tests.

## Pasos esperados

Definir contrato público, resolver ownership sin filtrar existencia y ejecutar query paginada.

## Restricciones

- No exponer hashes, tokens ni contenido anterior completo si basta un diff seguro.

## Tests y validaciones

Cubrir aislamiento, 3 por página, orden, no-op ausente y nota eliminada.

## Criterios de aceptación

- Solo el propietario ve un historial completo, ordenado y paginado de cambios reales.

## Resultado esperado

Trazabilidad funcional consumible. Detenerse antes de TASK-20.
