# TASK-09 — Login

Status: Planned

Prerequisites:

- [TASK-08](TASK-08-user-registration.md)

Next:

- [TASK-10](TASK-10-session-renewal-and-logout.md)

## Contexto y objetivo

Implementar `POST /api/v1/auth/login` con access token, refresh token y auditoría.

## Alcance

- Autenticar email y contraseña con mensaje genérico de fallo.
- Rechazar usuarios inactivos.
- Crear sesión persistente con hash del refresh token.
- Emitir access y refresh tokens según configuración.
- Auditar éxito y fallo sin credenciales.

## Fuera de alcance

- Refresh, logout, recuperación y rate limiting distribuido.

## Archivos probablemente afectados

- Servicio de sesión, ruta, persistencia y tests.

## Pasos esperados

Definir transacción, verificar credenciales, crear sesión, emitir tokens y probar ramas de error.

## Restricciones

- No distinguir públicamente usuario inexistente de contraseña incorrecta; no guardar refresh plano.

## Tests y validaciones

Cubrir éxito, credenciales inválidas, usuario inactivo, expiración configurada y rollback.

## Criterios de aceptación

- Solo credenciales válidas generan una sesión; logs y auditoría no contienen secretos.

## Resultado esperado

Login funcional. Detenerse antes de TASK-10.
