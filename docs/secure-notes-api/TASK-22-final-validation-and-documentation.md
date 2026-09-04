# TASK-22 — Validación final y documentación

Status: Planned

Prerequisites:

- [TASK-21](TASK-21-operational-hardening.md)

Next:

- Ninguna tarea de aplicación; seguirá planificación separada de containerización.

## Contexto y objetivo

Cerrar V1 mediante suite completa, prueba manual y documentación operativa reproducible.

## Alcance

- Ejecutar unit, HTTP e integración sobre PostgreSQL.
- Crear base vacía mediante migraciones y validar rollback previsto.
- Probar manualmente auth, perfiles, admin, notas e historial.
- Actualizar README con configuración, migraciones, contratos, permisos, estados, errores y comandos.
- Verificar cobertura crítica, lockfile, formato, análisis y ausencia de secretos.

## Fuera de alcance

- Nuevas funciones, frontend, Docker, Kubernetes, CI/CD y despliegue.

## Archivos probablemente afectados

- Tests faltantes, README, `.env.example` y correcciones demostradas.

## Pasos esperados

Comparar implementación con tareas, ejecutar suite completa, probar flujos y documentar límites reales.

## Restricciones

- No usar SQLite para sustituir PostgreSQL relevante ni presentar comandos no ejecutados como validados.

## Tests y validaciones

Ejecutar `uv lock --check`, pytest, cobertura acordada, ruff, migraciones y prueba manual completa.

## Criterios de aceptación

- Contratos, seguridad, permisos, atomicidad, paginación 9/3 y documentación quedan validados desde un entorno limpio.

## Resultado esperado

Secure Notes API V1 lista para planificar containerización y despliegue.

## Commit sugerido

No ejecutar automáticamente sin autorización:

```text
feat(secure-notes-api): implementar autenticación y notas privadas
```

## Siguiente tarea

Detenerse. No iniciar infraestructura sin una tarea aprobada.
