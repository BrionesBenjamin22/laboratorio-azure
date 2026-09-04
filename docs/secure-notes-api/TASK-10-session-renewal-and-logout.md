# TASK-10 — Renovación y logout

Status: Planned

Prerequisites:

- [TASK-09](TASK-09-login.md)

Next:

- [TASK-11](TASK-11-current-user-profile.md)

## Contexto y objetivo

Completar el lifecycle de sesión con `POST /auth/refresh` y `POST /auth/logout`.

## Alcance

- Validar hash, expiración, revocación y usuario activo.
- Rotar refresh token y revocar el anterior atómicamente.
- Detectar reutilización según la estrategia documentada.
- Revocar sesión actual mediante logout idempotente.
- Auditar renovación, revocación y rechazo.

## Fuera de alcance

- Revocar todas las sesiones, cookies browser y recuperación de cuenta.

## Archivos probablemente afectados

- Servicio de sesión, rutas, repositorio y tests.

## Pasos esperados

Implementar rotación transaccional, invalidar tokens previos y cubrir condiciones concurrentes relevantes.

## Restricciones

- Nunca persistir o registrar tokens planos; logout no revela estado previo.

## Tests y validaciones

Cubrir rotación, expiración, revocación, reutilización, usuario inactivo e idempotencia.

## Criterios de aceptación

- Un refresh usado deja de ser válido y logout revoca la sesión de forma segura.

## Resultado esperado

Lifecycle de sesión completo. Detenerse antes de TASK-11.
