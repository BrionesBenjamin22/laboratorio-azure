# TASK-05 — PostgreSQL, migraciones y readiness

Status: Planned

Prerequisites:

- [TASK-04](TASK-04-http-contract-and-middleware.md)

Next:

- [TASK-06](TASK-06-user-persistence.md)

## Contexto y objetivo

Incorporar persistencia real y migraciones antes de crear entidades.

## Alcance

- Seleccionar y documentar ORM, driver y herramienta de migraciones compatibles.
- Configurar pool y sesiones mediante `DATABASE_URL`.
- Delimitar transacciones por caso de uso.
- Crear migración base vacía o metadata mínima justificada.
- Implementar `/health/ready` con operación PostgreSQL acotada por timeout.
- Preparar tests de integración contra PostgreSQL aislado.

## Fuera de alcance

- Tablas de usuarios, sesiones o notas; SQLite como sustituto de pruebas relevantes.

## Archivos probablemente afectados

- Dependencias, configuración, persistencia, migraciones, health y tests.

## Pasos esperados

Verificar documentación oficial, elegir stack, conectar por lifespan, configurar migraciones y probar readiness disponible/no disponible.

## Restricciones

- No ejecutar cambios de esquema implícitos al iniciar la app ni registrar URL con credenciales.

## Tests y validaciones

Ejecutar lock, suite, calidad y migraciones upgrade/downgrade sobre PostgreSQL temporal.

## Criterios de aceptación

- Conexión cerrada correctamente, migraciones reproducibles y readiness refleja solo configuración/base interna.

## Resultado esperado

Fundación PostgreSQL lista. Detenerse antes de TASK-06.
