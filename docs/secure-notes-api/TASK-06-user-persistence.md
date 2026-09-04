# TASK-06 — Persistencia de usuarios y auditoría

Status: Planned

Prerequisites:

- [TASK-05](TASK-05-postgresql-and-migrations.md)

Next:

- [TASK-07](TASK-07-authentication-primitives.md)

## Contexto y objetivo

Crear el modelo persistente de usuarios y la base de auditoría antes de autenticar.

## Alcance

- Migración de usuario con UUID, email normalizado único, hash, rol, estado, timestamps y versión.
- Tabla de historial de usuario para creación y diferencias de campos seguras.
- Tabla de auditoría de seguridad separada y sin datos sensibles.
- Repositorios mínimos para buscar, crear y actualizar estado.
- Índices y restricciones en PostgreSQL.

## Fuera de alcance

- Password hashing, JWT, endpoints y notas.

## Archivos probablemente afectados

- Modelos, repositorios, migraciones y tests de integración.

## Pasos esperados

Definir invariantes, escribir migraciones, implementar acceso a datos y probar unicidad, rollback y queries.

## Restricciones

- No devolver modelos ORM como contratos; no almacenar contraseñas planas.

## Tests y validaciones

Ejecutar migraciones y suite sobre PostgreSQL; verificar constraints e índices.

## Criterios de aceptación

- Esquema reproducible, email único normalizado e historial de usuario separado de la auditoría de seguridad.

## Resultado esperado

Persistencia de identidad lista. Detenerse antes de TASK-07.
