# TASK-13 — Persistencia de notas e historial

Status: Planned

Prerequisites:

- [TASK-12](TASK-12-user-administration.md)

Next:

- [TASK-14](TASK-14-create-note.md)

## Contexto y objetivo

Crear esquema y repositorios de notas e historial antes de exponer operaciones.

## Alcance

- Migrar nota con propietario, título, contenido, timestamps, versión y `deleted_at`.
- Migrar historial con actor, evento, diferencias, versión, fecha y request ID.
- Definir claves foráneas, índices y política de conservación.
- Implementar repositorios con filtro por propietario y estado activo.
- Establecer helper transaccional para cambio más historial.

## Fuera de alcance

- Endpoints y operaciones CRUD públicas.
- Compartir notas o relaciones adicionales.

## Archivos probablemente afectados

- Modelos, repositorios, migraciones y tests PostgreSQL.

## Pasos esperados

Definir invariantes, crear migración, implementar acceso mínimo y probar constraints y rollback.

## Restricciones

- `owner_id` es inmutable; contenido e historial no se registran en logs.

## Tests y validaciones

Probar migraciones, índices, ownership, soft-delete filters y atomicidad básica.

## Criterios de aceptación

- Esquema reproducible y preparado para escribir cambio e historial en una transacción.

## Resultado esperado

Persistencia de notas lista. Detenerse antes de TASK-14.
