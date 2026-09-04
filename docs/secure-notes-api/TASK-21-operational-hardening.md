# TASK-21 — Hardening transaccional y operativo

Status: Planned

Prerequisites:

- [TASK-20](TASK-20-authentication-hardening.md)

Next:

- [TASK-22](TASK-22-final-validation-and-documentation.md)

## Contexto y objetivo

Revisar integridad, rendimiento y lifecycle sin agregar funcionalidad.

## Alcance

- Auditar límites de transacción, rollback y concurrencia optimista.
- Revisar índices, paginación y ausencia de N+1.
- Validar pool, timeouts, shutdown y readiness.
- Revisar tamaños de entrada y respuestas seguras.
- Confirmar migraciones forward y rollback soportado.
- Buscar secretos, contenido de notas y SQL sensible en logs.

## Fuera de alcance

- Nuevos endpoints, Redis, colas, tracing, Docker y Kubernetes.

## Archivos probablemente afectados

- Persistencia, configuración, lifecycle y tests según hallazgos.

## Pasos esperados

Seguir transacciones críticas, explicar planes de queries relevantes y corregir solo problemas comprobados.

## Restricciones

- No optimizar especulativamente ni sumar infraestructura preventiva.

## Tests y validaciones

Ejecutar suite PostgreSQL, concurrencia, migraciones, calidad y pruebas manuales de shutdown/readiness.

## Criterios de aceptación

- Cambios e historiales son atómicos; consultas paginadas usan índices; lifecycle es predecible.

## Resultado esperado

Servicio endurecido operativamente. Detenerse antes de TASK-22.
