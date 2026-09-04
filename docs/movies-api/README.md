# Movies API

Status: Planned

## Propósito

Construir una API pequeña en Go que consulte TMDB y exponga películas populares, búsqueda y detalle mediante un contrato HTTP propio. El módulo sirve principalmente para aprender Go y producir una carga stateless adecuada para prácticas posteriores de containerización y Kubernetes.

## Tecnología y filosofía

- Go con la versión estable disponible en el entorno del laboratorio.
- Biblioteca estándar como primera opción: `net/http`, `encoding/json`, `context`, `errors`, `http.Client` y `log/slog`.
- Sin Gin, Fiber, Echo, frameworks de inyección de dependencias, logging externo ni frameworks de testing salvo que una tarea futura demuestre una necesidad concreta.
- Interfaces pequeñas definidas cerca del consumidor, únicamente cuando exista más de una implementación real o se necesite sustituir una dependencia en pruebas.

## Arquitectura conceptual evolutiva

La estructura crecerá cuando aparezcan responsabilidades reales. No se debe crear desde el inicio todo el árbol final.

Piezas previstas durante el módulo:

- punto de entrada del proceso;
- configuración;
- servidor y handlers HTTP;
- casos de uso de películas;
- adaptador TMDB;
- modelos internos y DTO externos donde la separación sea necesaria.

No se utilizará `internal/platform/` como contenedor genérico. El logging puede configurarse inicialmente junto al arranque; solo tendrá paquete propio si adquiere una responsabilidad concreta. Una posible evolución es `internal/httpapi`, `internal/movies` e `internal/tmdb`, sin que esos nombres sean obligatorios si la implementación descubre una organización más simple.

## Contrato V1 previsto

| Método | Ruta | Finalidad |
| --- | --- | --- |
| `GET` | `/health/live` | Confirmar que el proceso HTTP está activo. |
| `GET` | `/health/ready` | Confirmar configuración e inicialización internas. |
| `GET` | `/api/v1/movies/popular?page=1` | Obtener hasta 9 películas de una página de TMDB. |
| `GET` | `/api/v1/movies/search?query=matrix&page=1` | Buscar y devolver hasta 9 resultados de una página de TMDB. |
| `GET` | `/api/v1/movies/{movie_id}` | Consultar el detalle normalizado de una película. |

`page_size` y `language` no forman parte del contrato V1.

Los listados utilizarán un envelope estable:

```json
{
  "data": [],
  "meta": {
    "page": 1,
    "returned": 9,
    "has_next": true
  }
}
```

Los errores utilizarán un envelope mínimo:

```json
{
  "error": {
    "code": "PROVIDER_UNAVAILABLE",
    "message": "Lo sentimos. No pudimos recuperar la información. Intente nuevamente.",
    "request_id": "identificador-de-la-solicitud"
  }
}
```

El modelo público de película se limitará inicialmente a los campos que consuman los tres endpoints: identificador, títulos, resumen, fecha de estreno, referencias de imágenes, valoración y géneros cuando estén disponibles. Los DTO de TMDB no forman parte del contrato público.

## Decisiones principales

### Paginación

`page` identifica directamente una página del proveedor. La aplicación solicitará esa página y devolverá como máximo sus primeros 9 resultados.

Esto implica que `page=2` solicita la segunda página de TMDB y no el segundo bloque matemáticamente continuo de nueve resultados. Si TMDB entrega más de 9 elementos por página, los elementos restantes no aparecerán al avanzar a la página siguiente. Esta limitación se acepta para mantener simple la primera versión.

La metadata V1 incluirá como mínimo:

- `page`: página solicitada al proveedor;
- `returned`: cantidad entregada, entre 0 y 9;
- `has_next`: indica si el proveedor informa otra página disponible.

No se combinarán varias páginas ni se aceptará `page_size`. Una paginación local continua podrá evaluarse en una versión futura.

### Idioma

El idioma se configura mediante `TMDB_DEFAULT_LANGUAGE`, inicialmente `es-AR`. No se acepta por request y no se replica una lista propia de idiomas de TMDB. La selección dinámica queda fuera de V1.

### Health checks

- `live` comprueba únicamente que el proceso HTTP responde.
- `ready` comprueba configuración crítica y dependencias internas inicializadas.
- `ready` no consulta TMDB ni depende de su disponibilidad instantánea. Una caída externa no debe provocar que Kubernetes retire todos los Pods.

### Lifecycle

El proceso atenderá señales de terminación, dejará de aceptar trabajo indefinidamente y dará un plazo limitado a las solicitudes activas para finalizar. La relación futura será Kubernetes → `SIGTERM` → aplicación Go → graceful shutdown.

### Seguridad y CORS

V1 valida `page`, `query` y `movie_id`, aplica timeouts, protege el token y devuelve errores seguros. No incorpora abstracciones de rate limiting, CORS ni headers orientados principalmente a documentos HTML. Esas decisiones se revisarán cuando existan Ingress, gateway o consumidor browser.

### Errores

El conjunto inicial se limita a:

- recurso no encontrado;
- proveedor no autorizado;
- proveedor limitado por cuota;
- timeout;
- proveedor no disponible;
- respuesta inválida del proveedor.

La capa HTTP traduce estos errores al contrato público sin exponer cuerpos de TMDB. No se diseñará una jerarquía compleja.

### Logging

Se utilizará `log/slog`.

Logs de request:

- `request_id`;
- método;
- ruta;
- estado;
- `duration_ms`.

Logs del proveedor:

- proveedor;
- operación;
- estado;
- `duration_ms`.

No se registrarán tokens, `Authorization`, secretos ni payloads completos. OpenTelemetry queda para una fase posterior.

### Autenticación e historial

V1 ofrece consultas de solo lectura y no administra entidades propias mutables. No requiere autenticación, permisos por rol ni endpoint de historial de cambios. Los logs operativos no sustituyen un historial de dominio.

## Variables previstas

```dotenv
APP_ENV=development
HTTP_PORT=8080
HTTP_READ_TIMEOUT_SECONDS=5
HTTP_WRITE_TIMEOUT_SECONDS=10
HTTP_IDLE_TIMEOUT_SECONDS=60
SHUTDOWN_TIMEOUT_SECONDS=10
TMDB_BASE_URL=
TMDB_API_TOKEN=
TMDB_DEFAULT_LANGUAGE=es-AR
TMDB_TIMEOUT_SECONDS=5
LOG_LEVEL=info
```

Los nombres podrán simplificarse durante las tareas si no todos resultan necesarios. `.env.example` nunca contendrá secretos reales.

## Mapa de tareas

| Orden | Tarea | Estado | Resultado |
| --- | --- | --- | --- |
| 1 | [TASK-01 — Inicialización del módulo Go](TASK-01-go-module-initialization.md) | `Planned` | Módulo Go mínimo y verificable. |
| 2 | [TASK-02 — Servidor HTTP base](TASK-02-base-http-server.md) | `Planned` | Servidor standard library con `live`. |
| 3 | [TASK-03 — Configuración y lifecycle](TASK-03-configuration-and-lifecycle.md) | `Planned` | Configuración, `ready`, `slog` y shutdown. |
| 4 | [TASK-04 — Contrato y middleware HTTP](TASK-04-http-contract-and-middleware.md) | `Planned` | Errores uniformes, `request_id` y logging HTTP. |
| 5 | [TASK-05 — Adaptador TMDB](TASK-05-tmdb-adapter.md) | `Planned` | Cliente externo reutilizable y probado. |
| 6 | [TASK-06 — Películas populares](TASK-06-popular-movies.md) | `Planned` | Primer flujo funcional completo. |
| 7 | [TASK-07 — Búsqueda de películas](TASK-07-search-movies.md) | `Planned` | Búsqueda validada. |
| 8 | [TASK-08 — Detalle de película](TASK-08-movie-detail.md) | `Planned` | Detalle y mapeo de no encontrado. |
| 9 | [TASK-09 — Hardening](TASK-09-hardening.md) | `Planned` | Revisión integral sin funcionalidad nueva. |
| 10 | [TASK-10 — Suite final y documentación](TASK-10-final-validation-and-documentation.md) | `Planned` | Módulo validado y documentado. |

El orden es deliberadamente lineal para introducir una revisión humana entre incrementos. TASK-05 crea el cliente concreto; la interfaz mínima del consumidor aparece recién en TASK-06, cuando existe el primer caso de uso que la necesita.

## Fuera de alcance

- persistencia, usuarios y favoritos;
- frontend y decisiones de navegación o presentación;
- CORS antes de disponer de un consumidor browser;
- rate limiting anticipado;
- OpenTelemetry y observabilidad distribuida;
- caché distribuida;
- Dockerfile e imágenes;
- manifiestos Kubernetes;
- CI/CD y despliegues.

## Etapas futuras relacionadas con Kubernetes

Después de completar y probar la aplicación se planificarán, por separado:

- construcción de una imagen OCI;
- configuración mediante ConfigMap y Secret;
- probes basadas en `live` y `ready`;
- terminación coordinada con `SIGTERM` y período de gracia;
- Deployment, Service, réplicas y recursos;
- Horizontal Pod Autoscaler;
- políticas de red, Ingress y rate limiting en la capa adecuada;
- métricas, trazas y dashboards.
