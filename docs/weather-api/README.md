# Weather API

Status: Planned

## Propósito

Construir una API stateless en Python y FastAPI que consulte el clima actual por ciudad mediante un proveedor externo. El módulo permitirá aprender FastAPI, programación asíncrona, configuración y adaptación de APIs sin convertir el servicio en un producto meteorológico completo.

## Tecnología y principios

- Python 3.11 o una versión compatible verificada al iniciar la tarea.
- FastAPI y servidor ASGI.
- `uv` como administrador del proyecto y lockfile.
- Cliente HTTP asíncrono introducido cuando exista el adaptador.
- `pytest` y herramientas de prueba necesarias, sin framework de mocks adicional.
- Logging estándar de Python; dependencias adicionales solo ante una necesidad concreta.
- Proveedor y caché detrás de responsabilidades propias, sin capas genéricas de utilidades.

## Arquitectura evolutiva

El proyecto comenzará con paquete de aplicación y tests. Las áreas de configuración, HTTP, dominio y proveedor se separarán solo cuando aparezcan. No se creará desde TASK-01 toda la estructura final ni un paquete genérico `utils`.

El cliente del proveedor se cerrará mediante el lifespan de FastAPI. El servidor ASGI administrará señales y graceful shutdown; la aplicación debe liberar sus recursos dentro de ese lifecycle.

## Contrato V1 previsto

| Método | Ruta | Finalidad |
| --- | --- | --- |
| `GET` | `/health/live` | Confirmar que el proceso responde. |
| `GET` | `/health/ready` | Confirmar configuración e inicialización internas. |
| `GET` | `/api/v1/weather/current?city=Rosario&country_code=AR` | Consultar el clima actual normalizado. |

V1 acepta `city` y `country_code` opcional. El sistema de unidades y el idioma son configuración interna, no parámetros por request. Inicialmente se utilizará sistema métrico e idioma `es-AR`, siempre que el proveedor seleccionado los soporte.

Respuesta funcional mínima:

```json
{
  "data": {
    "location": {
      "city": "Rosario",
      "country_code": "AR"
    },
    "observed_at": "2026-01-01T12:00:00Z",
    "temperature": 25.4,
    "temperature_unit": "celsius",
    "feels_like": 26.1,
    "humidity_percent": 60,
    "wind_speed": 12.0,
    "wind_speed_unit": "km_h",
    "condition": "partly_cloudy"
  }
}
```

Los campos opcionales se representarán consistentemente y nunca se completarán con valores ficticios. El DTO del proveedor no forma parte del contrato público.

## Decisiones principales

### Selección del proveedor

La elección se realiza en una tarea separada antes de implementar el cliente. Debe considerar documentación oficial, plan gratuito, autenticación, localización, unidades, límites y estabilidad. La decisión quedará registrada sin credenciales.

### Readiness

`ready` valida configuración y recursos internos inicializados, incluido el cliente HTTP. No consulta al proveedor ni depende de su disponibilidad instantánea.

### Caché

Después del primer flujo funcional se agregará una caché en memoria, pequeña y acotada. Su clave utilizará ciudad normalizada y país. No se introduce Redis ni una abstracción distribuida en V1.

### Seguridad

Se validarán longitudes, caracteres de control y código de país. La clave externa nunca aparecerá en logs o respuestas. CORS y rate limiting se analizarán más adelante junto con frontend, Ingress o gateway; no se anticipan en este módulo.

### Historial y permisos

El servicio no administra entidades mutables ni datos de usuario. No requiere autenticación, roles, soft delete o historial de cambios.

## Variables previstas

```dotenv
APP_ENV=development
HTTP_HOST=127.0.0.1
HTTP_PORT=8080
WEATHER_PROVIDER=
WEATHER_API_BASE_URL=
WEATHER_API_KEY=
WEATHER_DEFAULT_LANGUAGE=es-AR
WEATHER_UNITS=metric
WEATHER_TIMEOUT_SECONDS=5
WEATHER_CACHE_TTL_SECONDS=300
WEATHER_CACHE_MAX_ENTRIES=256
LOG_LEVEL=INFO
```

Solo se incorporarán variables cuando una tarea las consuma. `.env.example` no tendrá secretos reales.

## Mapa de tareas

| Orden | Tarea | Estado | Resultado |
| --- | --- | --- | --- |
| 1 | [TASK-01 — Inicialización Python](TASK-01-python-project-initialization.md) | `Planned` | Proyecto y calidad mínimos. |
| 2 | [TASK-02 — Aplicación FastAPI base](TASK-02-base-fastapi-application.md) | `Planned` | App con liveness. |
| 3 | [TASK-03 — Configuración y lifecycle](TASK-03-configuration-and-lifecycle.md) | `Planned` | Configuración, readiness y cierre de recursos. |
| 4 | [TASK-04 — Contrato y middleware HTTP](TASK-04-http-contract-and-middleware.md) | `Planned` | Errores, request ID y logging. |
| 5 | [TASK-05 — Selección y contrato del proveedor](TASK-05-provider-selection.md) | `Planned` | Decisión documentada y modelo interno. |
| 6 | [TASK-06 — Adaptador meteorológico](TASK-06-weather-provider-adapter.md) | `Planned` | Cliente asíncrono probado. |
| 7 | [TASK-07 — Clima actual](TASK-07-current-weather.md) | `Planned` | Flujo funcional completo. |
| 8 | [TASK-08 — Caché local](TASK-08-local-cache.md) | `Planned` | Caché TTL acotada. |
| 9 | [TASK-09 — Hardening](TASK-09-hardening.md) | `Planned` | Revisión operativa y de seguridad. |
| 10 | [TASK-10 — Validación final y documentación](TASK-10-final-validation-and-documentation.md) | `Planned` | Módulo probado y documentado. |

## Fuera de alcance

- pronóstico, alertas e historial meteorológico;
- autenticación y persistencia;
- unidades o idioma por request;
- múltiples proveedores activos o fallback;
- Redis;
- frontend y CORS;
- Dockerfile, Kubernetes, CI/CD y Azure.

## Etapas futuras de infraestructura

La planificación posterior deberá cubrir imagen OCI, Secret para la API key, ConfigMap, probes, réplicas, escalamiento, políticas de red, comportamiento ante rate limit y observabilidad. No se implementan en estas tareas.
