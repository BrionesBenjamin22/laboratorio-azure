# Weather API

Status: Draft

## Propósito

Planificar una API stateless en Python y FastAPI que consulte información meteorológica actual mediante un proveedor externo.

## Estado de planificación

La especificación amplia existente fue trasladada a [PLANNING-DRAFT.md](PLANNING-DRAFT.md) para conservar todas sus decisiones. No debe ejecutarse como una única tarea: requiere una revisión crítica y un desglose propio antes de comenzar la implementación.

## Tecnología prevista

- Python;
- FastAPI;
- cliente HTTP asíncrono;
- proveedor meteorológico por seleccionar;
- configuración externa para credenciales, timeouts y caché.

## Artefactos y tareas

| Documento | Estado | Finalidad |
| --- | --- | --- |
| [Borrador de planificación](PLANNING-DRAFT.md) | `Draft` | Fuente existente que deberá revisarse y dividirse. |

Todavía no existen tareas de implementación aprobadas. El futuro desglose debe responder a las necesidades propias de FastAPI, configuración, integración externa, Secrets, ConfigMaps y caché, sin copiar mecánicamente la secuencia de Movies API.

## Fuera de alcance de esta reorganización

- seleccionar el proveedor meteorológico;
- decidir o instalar dependencias;
- diseñar el desglose completo;
- desarrollar endpoints;
- crear infraestructura.
