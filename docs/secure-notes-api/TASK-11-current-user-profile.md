# TASK-11 — Perfil propio

Status: Planned

Prerequisites:

- [TASK-10](TASK-10-session-renewal-and-logout.md)

Next:

- [TASK-12](TASK-12-user-administration.md)

## Contexto y objetivo

Incorporar autenticación reutilizable, `GET/PATCH /api/v1/users/me` y consulta del historial propio.

## Alcance

- Resolver usuario desde access token y comprobar estado.
- Devolver campos seguros del perfil.
- Actualizar email mediante diferencias reales y versión esperada.
- No escribir si no existen cambios.
- Registrar historial y auditar cambios de perfil sin datos sensibles.
- Implementar `GET /api/v1/users/me/history?page=1` con 3 eventos por página.

## Fuera de alcance

- Cambio de contraseña, rol, baja de cuenta y administración.

## Archivos probablemente afectados

- Dependencia de auth, schemas, servicio, rutas y tests.

## Pasos esperados

Crear dependencia pequeña de usuario actual, implementar lectura y patch transaccional, y mapear conflicto.

## Restricciones

- El cliente no modifica rol, estado, timestamps o versión directamente.

## Tests y validaciones

Cubrir token ausente/inválido, usuario inactivo, lectura, cambio, no-op, duplicado, versión obsoleta e historial paginado.

## Criterios de aceptación

- Solo el usuario activo accede; PATCH guarda únicamente diferencias reales y el historial propio se pagina de a 3.

## Resultado esperado

Perfil propio seguro. Detenerse antes de TASK-12.
