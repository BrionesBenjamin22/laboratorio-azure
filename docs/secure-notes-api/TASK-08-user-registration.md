# TASK-08 — Registro de usuarios

Status: Planned

Prerequisites:

- [TASK-07](TASK-07-authentication-primitives.md)

Next:

- [TASK-09](TASK-09-login.md)

## Contexto y objetivo

Crear el primer flujo de identidad: `POST /api/v1/auth/register`.

## Alcance

- Validar y normalizar email y contraseña.
- Crear siempre rol `user`, ignorando o rechazando rol enviado.
- Persistir hash, historial de creación y evento de auditoría en una transacción.
- Responder `201` con datos seguros y mensaje de éxito.
- Mapear email duplicado a `409`.

## Fuera de alcance

- Login, verificación de correo, admin y emisión de tokens.

## Archivos probablemente afectados

- Schemas, servicio, ruta, repositorio y tests.

## Pasos esperados

Definir contrato, validar antes de persistir, ejecutar transacción y cubrir errores.

## Restricciones

- Nunca aceptar rol público ni devolver hash; evitar enumeración adicional innecesaria.

## Tests y validaciones

Cubrir éxito, email inválido/duplicado, contraseña inválida, rol malicioso y rollback conjunto de usuario, historial y auditoría.

## Criterios de aceptación

- Usuario, historial y auditoría se crean atómicamente con rol `user` y sin datos sensibles.

## Resultado esperado

Registro seguro. Detenerse antes de TASK-09.
