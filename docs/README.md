# Documentación del laboratorio

## Propósito

Este directorio es el punto de entrada para la planificación técnica del laboratorio. La documentación se organiza por módulo para que cada cambio pueda analizarse, implementarse y validarse de forma acotada.

Las aplicaciones son cargas de trabajo educativas. La planificación debe favorecer el aprendizaje de cada tecnología y la experimentación posterior con infraestructura, sin convertir los servicios en productos innecesariamente complejos.

## Convención de organización

Los módulos utilizan nombres en minúsculas y kebab-case, alineados con sus carpetas en `apps/`:

- `docs/movies-api/` corresponde a `apps/movies-api/`;
- `docs/weather-api/` corresponde a `apps/weather-api/`;
- `docs/secure-notes-api/` corresponde a `apps/secure-notes-api/`.

Se eligió `movies-api` en lugar de `TMDBApi` porque identifica el servicio y no lo acopla nominalmente a TMDB. El proveedor externo podrá cambiar sin exigir una reorganización documental.

## Estados de tareas

Cada tarea o borrador declara uno de estos estados:

| Estado | Significado |
| --- | --- |
| `Planned` | Definida y pendiente de ejecución. |
| `In Progress` | Actualmente en ejecución. |
| `Done` | Implementada y validada. |
| `Blocked` | No puede continuar sin una decisión o dependencia externa. |
| `Draft` | Contenido preliminar que todavía debe revisarse y dividirse antes de ejecutarse. |

Los estados se actualizan directamente en Markdown. No se incorporará tooling adicional para administrarlos.

## Módulos

| Módulo | Tecnología | Tipo | Planificación |
| --- | --- | --- | --- |
| [Movies API](movies-api/README.md) | Go y TMDB | Stateless | Especificación revisada y tareas ejecutables disponibles. |
| [Weather API](weather-api/README.md) | Python, FastAPI y proveedor meteorológico | Stateless | Especificación revisada y tareas ejecutables disponibles. |
| [Secure Notes API](secure-notes-api/README.md) | Python, FastAPI y PostgreSQL | Stateful | Especificación revisada y tareas ejecutables disponibles. |

## Documentación global

- [Equipo de agentes](agents/README.md): responsabilidades, scopes y protocolo de coordinación para ejecutar las tareas.
- [Bootstrap inicial del repositorio](00-repository-bootstrap.md): tarea histórica de preparación del monorepo.
- Containerización: planificación pendiente después de probar localmente las aplicaciones.
- Kubernetes: planificación pendiente después de containerizar los servicios.
- Observabilidad: planificación pendiente para logs, métricas y trazas en el entorno desplegado.
- CI/CD: planificación pendiente después del primer despliegue manual.
- Frontends: planificación independiente pendiente; las decisiones de interfaz no forman parte de los contratos backend actuales.

No se crean documentos vacíos para estas áreas. Se incorporarán cuando exista alcance suficiente para definir una tarea ejecutable.

## Convenciones backend compartidas

- Versionar los endpoints funcionales bajo `/api/v1`.
- Reservar `/health/live` y `/health/ready` para salud del proceso y preparación interna.
- Usar JSON, campos en `snake_case` y fechas UTC en formato ISO 8601.
- Mantener errores públicos desacoplados de proveedores y persistencia.
- Incluir `request_id` en logs y respuestas de error.
- No registrar credenciales, tokens, headers de autorización ni contenido privado.
- Configurar dependencias mediante variables de entorno y documentarlas en `.env.example`.
- Mantener timeouts y propagación de cancelación en operaciones externas.
- Agregar dependencias y abstracciones solo cuando resuelvan una responsabilidad actual.

Los listados destinados a futuros homes deberán admitir como máximo 9 elementos por página. Los historiales de cambios, cuando existan, utilizarán 3 elementos por página. Cada módulo debe documentar su semántica de paginación concreta; no se asumirá que proveedores externos usan el mismo tamaño de página.

## Regla de ejecución

Una tarea debe permitir que un agente:

1. inspeccione el estado existente;
2. implemente un único incremento coherente;
3. ejecute sus validaciones;
4. informe cambios y limitaciones;
5. se detenga antes de la siguiente tarea.

No se debe ejecutar una tarea marcada como `Draft`. Al finalizar un módulo se realizará una prueba manual y la suite backend correspondiente. Los commits solo se ejecutarán con autorización explícita del usuario.
