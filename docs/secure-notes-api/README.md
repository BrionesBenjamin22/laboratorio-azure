# Secure Notes API

Status: Draft

## Propósito

Planificar una API stateful en Python y FastAPI para autenticación y notas privadas persistidas en PostgreSQL.

## Estado de planificación

La especificación amplia existente fue trasladada a [PLANNING-DRAFT.md](PLANNING-DRAFT.md) para conservar modelos, contratos y requisitos de seguridad. No debe ejecutarse como una única tarea: requiere revisión y un desglose específico antes de implementar.

## Tecnología prevista

- Python;
- FastAPI;
- PostgreSQL;
- migraciones;
- JWT;
- autorización por propietario y rol;
- auditoría, historial y soft delete.

## Artefactos y tareas

| Documento | Estado | Finalidad |
| --- | --- | --- |
| [Borrador de planificación](PLANNING-DRAFT.md) | `Draft` | Fuente existente que deberá revisarse y dividirse. |

Todavía no existen tareas de implementación aprobadas. El futuro desglose deberá separar base de datos, migraciones, autenticación, autorización, CRUD, historial y pruebas según sus dependencias reales.

## Fuera de alcance de esta reorganización

- seleccionar ORM o bibliotecas JWT;
- decidir parámetros criptográficos;
- diseñar el desglose completo;
- desarrollar endpoints;
- crear base de datos o infraestructura.
