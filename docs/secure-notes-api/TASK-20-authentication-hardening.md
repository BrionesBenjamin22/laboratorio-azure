# TASK-20 — Hardening de autenticación

Status: Planned

Prerequisites:

- [TASK-19](TASK-19-note-history.md)

Next:

- [TASK-21](TASK-21-operational-hardening.md)

## Contexto y objetivo

Auditar autenticación, autorización y auditoría con todos los flujos disponibles.

## Alcance

- Revisar hashing, claims, expiración, rotación y revocación.
- Revisar enumeración de usuarios y mensajes públicos.
- Comprobar autorización por estado, rol y ownership en todos los endpoints.
- Revisar provisión offline de admin.
- Verificar auditoría de éxitos, fallos y denegaciones.
- Definir protección básica ante intentos repetidos sin anticipar infraestructura distribuida.

## Fuera de alcance

- MFA, OAuth, recuperación, WAF y rate limiting distribuido.

## Archivos probablemente afectados

- Auth, autorización, auditoría y tests; documentación ante hallazgos.

## Pasos esperados

Construir matriz de permisos, simular abuso, buscar filtraciones y corregir hallazgos reproducibles.

## Restricciones

- No relajar privacidad de notas para administradores ni introducir soluciones criptográficas propias.

## Tests y validaciones

Cubrir matriz completa, tokens manipulados/reutilizados, usuarios inactivos y logs sin secretos.

## Criterios de aceptación

- No existe escalación de rol, acceso cruzado ni material sensible en logs o auditoría.

## Resultado esperado

Superficie de autenticación endurecida. Detenerse antes de TASK-21.
