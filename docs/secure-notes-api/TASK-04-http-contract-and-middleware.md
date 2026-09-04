# TASK-04 — Contrato y middleware HTTP

Status: Planned

Prerequisites:

- [TASK-03](TASK-03-configuration-and-lifecycle.md)

Next:

- [TASK-05](TASK-05-postgresql-and-migrations.md)

## Contexto y objetivo

Definir el límite HTTP común antes de exponer operaciones con datos o autenticación.

## Alcance

- Envelope de éxito y error, códigos mínimos y mensajes seguros.
- `request_id` en contexto, header, logs y errores.
- Logging de método, ruta, status y duración.
- Manejo uniforme de validación y excepciones inesperadas.

## Fuera de alcance

- Errores de base, auth, CORS, rate limiting, métricas y tracing.

## Archivos probablemente afectados

- Middleware, handlers de excepción, contratos y tests HTTP.

## Pasos esperados

Definir contratos pequeños, integrar validación FastAPI, agregar correlación y probar modo no debug.

## Restricciones

- No registrar bodies o headers completos ni crear jerarquías complejas.

## Tests y validaciones

Ejecutar suite y calidad; cubrir éxito, validación, error inesperado y correlación.

## Criterios de aceptación

- Todas las respuestas de error son seguras, uniformes y correlacionables.

## Resultado esperado

Frontera HTTP estable. Detenerse antes de TASK-05.
