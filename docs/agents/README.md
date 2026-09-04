# Equipo de agentes

## Propósito

Este documento define la coordinación mínima para ejecutar incrementalmente las tareas de Movies API, Weather API y Secure Notes API. El equipo tiene cuatro roles y no incorpora jerarquías adicionales:

- `orchestrator`: coordina el flujo y mantiene la interacción con el usuario;
- `movies_api`: ejecuta tareas de Movies API;
- `weather_api`: ejecuta tareas de Weather API;
- `secure_notes_api`: ejecuta tareas de Secure Notes API.

Las definiciones ejecutables se encuentran en:

- [Orchestrator](../../.codex/agents/orchestrator.toml);
- [Movies API Agent](../../.codex/agents/movies_api.toml);
- [Weather API Agent](../../.codex/agents/weather_api.toml);
- [Secure Notes API Agent](../../.codex/agents/secure_notes_api.toml).

## Mecanismo

Codex carga agentes personalizados del proyecto desde `.codex/agents/*.toml`. Cada archivo declara `name`, `description` y `developer_instructions`. No se fijan modelos, nivel de razonamiento ni permisos: los cuatro agentes heredan la configuración de la sesión principal y sus restricciones se expresan mediante scopes de trabajo explícitos.

No se agrega `.codex/config.toml` porque el soporte multiagente está habilitado por defecto y el flujo inicial será secuencial. Tampoco se crean scripts para simular delegación.

## Responsabilidades

### Orchestrator

- Lee la documentación global, el README del módulo y la TASK candidata.
- Selecciona una sola tarea `Planned` cuyos prerequisites estén completos.
- Cambia el estado a `In Progress` al iniciar su ejecución.
- Delega al agente propietario del módulo.
- Revisa el `TASK REPORT` contra los criterios de aceptación.
- Actualiza el estado a `Done` o `Blocked` cuando corresponda.
- Informa al usuario, presenta decisiones importantes y se detiene antes de la siguiente tarea.
- Coordina cualquier modificación de archivos compartidos.

### Agentes de módulo

Cada agente:

- lee el README del módulo y la TASK asignada;
- analiza el estado existente y respeta cambios ajenos;
- implementa únicamente el incremento asignado;
- ejecuta los tests y validaciones requeridos;
- documenta decisiones comprendidas en su tarea;
- devuelve un informe al Orchestrator;
- se detiene sin elegir ni iniciar la siguiente tarea.

| Agente | Scope de escritura | Fuente principal |
| --- | --- | --- |
| `movies_api` | `apps/movies-api/**`, `docs/movies-api/**` | [Movies API](../movies-api/README.md) |
| `weather_api` | `apps/weather-api/**`, `docs/weather-api/**` | [Weather API](../weather-api/README.md) |
| `secure_notes_api` | `apps/secure-notes-api/**`, `docs/secure-notes-api/**` | [Secure Notes API](../secure-notes-api/README.md) |

Los agentes pueden leer documentación global. No pueden modificar `docs/README.md`, `README.md`, `.gitignore`, `Makefile`, `.github/**` ni otro archivo compartido sin coordinación del Orchestrator.

## Fuente de verdad

El orden de prioridad es:

1. instrucción explícita actual del usuario;
2. documentación aprobada del módulo;
3. TASK asignada;
4. documentación global;
5. convenciones idiomáticas del lenguaje;
6. decisión técnica razonable dentro del alcance.

Una contradicción importante entre la especificación y la TASK se escala al Orchestrator; el agente de módulo no elige arbitrariamente.

## Flujo de una tarea

```text
Usuario
   ↓
Orchestrator
   ↓
Agente del módulo
   ↓
Análisis e implementación
   ↓
Tests y validaciones
   ↓
TASK REPORT
   ↓
Orchestrator
   ↓
Usuario
```

Por defecto el flujo se detiene después del informe al usuario. La TASK siguiente solo comienza con autorización explícita. Una autorización futura de ejecución continua puede cambiar esta regla para el alcance autorizado.

## Estados

El Orchestrator administra los estados documentales existentes:

| Momento | Estado de la TASK |
| --- | --- |
| Pendiente y ejecutable | `Planned` |
| Delegada y en ejecución | `In Progress` |
| Implementada, validada y revisada | `Done` |
| Detenida por una decisión o dependencia | `Blocked` |

`FAILED` pertenece al informe de ejecución y no agrega un estado documental nuevo. Una tarea con fallo técnico no resuelto permanece `In Progress` mientras el Orchestrator informa el diagnóstico y acuerda el siguiente intento.

## Autoridad y escalamiento

El Orchestrator puede resolver convenciones idiomáticas, nombres internos evidentes, imports, formato, tests exigidos y correcciones menores cubiertas por la tarea. Debe consultar al usuario antes de:

- agregar un framework o una dependencia relevante no prevista;
- cambiar contratos HTTP, endpoints o alcance;
- cambiar persistencia, autenticación o arquitectura;
- contradecir una decisión documentada;
- eliminar funcionalidad;
- introducir infraestructura;
- modificar reglas compartidas entre módulos.

Un agente de módulo devuelve `BLOCKED` cuando necesita una decisión funcional importante, una dependencia no autorizada, un cambio de arquitectura, un archivo fuera de su scope o cuando encuentra criterios incompatibles. Un problema técnico trivial debe investigarse y corregirse dentro de la tarea. Si persiste después de intentos razonables, devuelve `FAILED` con el diagnóstico.

## Informe del agente de módulo

Cada ejecución debe finalizar con esta estructura:

```text
TASK REPORT

Module:
<módulo>

Task:
<identificador y título>

Status:
COMPLETED | BLOCKED | FAILED

Summary:
<resultado breve>

Files changed:
- <archivo o NONE>

Tests executed:
- <comando o NONE>

Validation results:
- <resultado>

Acceptance criteria:
- [PASS] <criterio>
- [FAIL] <criterio>

Decisions made:
- <decisión o NONE>

Important assumptions:
- <supuesto o NONE>

Problems found:
- <problema o NONE>

Decision required:
NONE | <explicación concreta>

Recommended next task:
TASK-XX | NONE

Suggested commit:
tipo(scope): descripción breve
```

El agente especializado informa al Orchestrator y no pregunta directamente al usuario.

## Informe del Orchestrator al usuario

Cuando una tarea se completa, el Orchestrator resume:

```text
## Tarea completada

Módulo:
...

Tarea:
...

Resultado:
...

Cambios principales:
- ...

Validaciones:
- ...

Siguiente tarea:
...

Decisiones necesarias:
Ninguna.

Commit sugerido:
tipo(scope): descripción breve

¿Deseas continuar con TASK-XX?
```

Si existe una decisión importante, explica contexto, opciones, ventajas, desventajas y recomendación. La parte afectada queda detenida hasta recibir respuesta.

## Git y concurrencia

- Los agentes pueden inspeccionar `git status`, `git diff` y `git log`.
- Ningún agente ejecuta commits sin autorización explícita del usuario.
- No se revierten cambios ajenos ni se usan `git reset --hard` o `git clean -fd`.
- El mensaje sugerido usa `tipo(scope): descripción breve`.
- El trabajo puede paralelizarse en el futuro solo entre módulos independientes.
- Los archivos compartidos permanecen bajo coordinación del Orchestrator incluso con paralelismo.

## Fases no anticipadas

Los agentes de aplicación no incorporan frontend, Docker, Kubernetes, Azure, CI/CD ni observabilidad distribuida hasta que una tarea aprobada lo indique. Sí conservan los requisitos ya documentados que preparan cada servicio como workload, incluidos configuración externa, salud, cierre ordenado, logging y statelessness cuando corresponda.
