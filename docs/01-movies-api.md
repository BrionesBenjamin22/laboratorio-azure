# Tarea 01: Movies API

## 1. Objetivo

Desarrollar `apps/movies-api` como un servicio HTTP en Go que consulte TMDB y exponga un contrato estable para buscar películas, obtener listados y consultar detalles.

El servicio será la primera aplicación del laboratorio y servirá para practicar Go, integración con una API externa, pruebas, configuración y diseño de una carga sin estado.

## 2. Alcance funcional

Implementar:

- listado de películas populares;
- búsqueda de películas por texto;
- consulta del detalle de una película;
- paginación propia de hasta 9 resultados por página;
- endpoints de salud;
- respuestas y errores según las convenciones de [`docs/README.md`](README.md).

No implementar en esta tarea:

- autenticación;
- usuarios o favoritos;
- persistencia;
- frontend;
- Dockerfile;
- manifiestos Kubernetes;
- CI/CD;
- despliegues en Azure.

## 3. Inicialización del módulo

Inicializar el módulo dentro de `apps/movies-api` con el nombre derivado del remote actual:

```powershell
cd apps/movies-api
go mod init github.com/BrionesBenjamin22/laboratorio-azure/apps/movies-api
```

```bash
cd apps/movies-api
go mod init github.com/BrionesBenjamin22/laboratorio-azure/apps/movies-api
```

No ejecutar este comando fuera de la carpeta del servicio.

## 4. Arquitectura esperada

La implementación debe mantener separadas las siguientes responsabilidades:

```text
apps/movies-api/
├── cmd/api/                 # Punto de entrada
├── internal/config/         # Variables de entorno
├── internal/http/           # Router, handlers y middleware
├── internal/model/          # Modelos internos y DTO
├── internal/service/        # Casos de uso
├── internal/provider/tmdb/  # Adaptador de TMDB
├── internal/platform/       # Logging y utilidades operativas
├── .env.example
├── go.mod
├── go.sum
└── README.md
```

La estructura podrá ajustarse si Go permite una alternativa más simple, pero debe conservar la separación entre HTTP, negocio e integración externa.

## 5. Contrato inicial

### `GET /health/live`

Confirma que el proceso está activo. No debe depender de TMDB.

Respuesta esperada: `200 OK`.

### `GET /health/ready`

Confirma que la configuración necesaria es válida. La estrategia para comprobar TMDB no debe provocar una llamada externa costosa en cada probe.

Respuestas esperadas:

- `200 OK` cuando el servicio puede atender solicitudes;
- `503 Service Unavailable` cuando falta configuración crítica.

### `GET /api/v1/movies/popular`

Parámetros:

- `page`: entero mayor o igual que 1; valor predeterminado 1;
- `page_size`: entero entre 1 y 9; valor predeterminado 9;
- `language`: valor opcional dentro de una lista admitida.

### `GET /api/v1/movies/search`

Parámetros:

- `query`: texto obligatorio, normalizado y con longitud acotada;
- `page`: entero mayor o igual que 1;
- `page_size`: entero entre 1 y 9;
- `language`: valor opcional admitido.

Una búsqueda vacía o inválida debe responder `400 Bad Request` sin llamar a TMDB.

### `GET /api/v1/movies/{movie_id}`

`movie_id` debe ser un entero positivo. Un identificador inexistente debe mapearse a `404 Not Found` sin exponer la respuesta original del proveedor.

## 6. Modelo público de película

El contrato propio no debe exponer directamente las estructuras de TMDB. Evaluar los siguientes campos:

- `id`;
- `title`;
- `original_title`;
- `overview`;
- `release_date`;
- `poster_path`;
- `backdrop_path`;
- `vote_average`;
- `genres`.

Los campos opcionales deberán representarse de forma consistente. Las URLs de imágenes, si se exponen, deben construirse mediante configuración y no quedar dispersas en handlers.

## 7. Integración con TMDB

- Definir una interfaz propia para el proveedor.
- Encapsular autenticación, URLs, parámetros y transformación de respuestas en el adaptador.
- Configurar URL base, token, idioma predeterminado y timeout mediante variables de entorno.
- Utilizar un cliente HTTP reutilizable con timeout; no crear un cliente por solicitud.
- Propagar cancelación desde el contexto HTTP.
- Mapear errores de red, timeout, rate limit, autorización y datos inválidos a errores internos tipados.
- No registrar el token ni headers sensibles.
- No inventar endpoints ni campos: verificar el contrato oficial de TMDB durante la implementación.

Variables mínimas previstas:

```dotenv
APP_ENV=development
HTTP_PORT=8080
TMDB_BASE_URL=
TMDB_API_TOKEN=
TMDB_DEFAULT_LANGUAGE=es-AR
TMDB_TIMEOUT_SECONDS=5
LOG_LEVEL=info
```

El `.env.example` no debe contener credenciales reales.

## 8. Paginación y rendimiento

- Entregar como máximo 9 películas por página aunque el proveedor utilice otro tamaño.
- Mantener metadatos de página consistentes con el contrato compartido.
- Evitar llamadas duplicadas dentro de una misma solicitud.
- Diseñar el servicio para permitir un adaptador de caché futuro sin acoplarlo al handler.
- No incorporar Redis ni otra infraestructura en esta primera versión.

## 9. Seguridad

- Validar identificadores, texto de búsqueda, idioma y paginación.
- Establecer límites de tamaño para solicitudes y respuestas.
- Configurar timeouts del servidor HTTP.
- Añadir headers de seguridad aplicables a una API.
- Mantener CORS deshabilitado o restringido mediante configuración; no aceptar cualquier origen por defecto.
- Evitar mensajes que permitan inferir tokens o detalles internos del proveedor.
- Preparar un punto de extensión para rate limiting futuro.

No existen permisos por rol en esta versión porque todos los recursos son consultas públicas de solo lectura. Esta decisión debe quedar documentada en el README del módulo.

## 10. Historial y auditoría

El servicio no administra entidades mutables propias, por lo que no corresponde implementar un historial de cambios.

Sí debe registrar eventos operativos mínimos:

- inicio y cierre del proceso;
- endpoint y estado HTTP;
- duración;
- resultado de la dependencia externa sin incluir payloads completos;
- `request_id`.

## 11. Pruebas

Implementar como mínimo:

- pruebas unitarias del servicio con un proveedor simulado;
- pruebas de validación y transformación de DTO;
- pruebas del adaptador TMDB mediante un servidor HTTP de prueba;
- pruebas de handlers para éxito, validación, no encontrado, timeout y error del proveedor;
- prueba de paginación con límites 1 y 9;
- prueba que confirme que errores inválidos no llaman al proveedor;
- ejecución de `go test ./...`;
- ejecución de `go test -race ./...` cuando el entorno lo permita;
- ejecución de `go vet ./...` y verificación con `gofmt`.

Las pruebas no deben depender de la API real de TMDB.

## 12. Documentación del módulo

Actualizar `apps/movies-api/README.md` con:

- requisitos;
- configuración;
- ejecución local;
- estructura interna;
- endpoints y ejemplos;
- comandos de prueba;
- errores esperados;
- decisiones de seguridad;
- limitaciones conocidas.

## 13. Criterios de aceptación

- El módulo Go utiliza la ruta correcta del repositorio.
- Los tres endpoints funcionales respetan el contrato versionado.
- Los listados nunca superan 9 elementos por página.
- La integración con TMDB está desacoplada y es sustituible en pruebas.
- Los errores son uniformes, seguros y contienen `request_id`.
- No existen secretos versionados.
- Los endpoints de salud diferencian proceso y preparación.
- Todas las pruebas y validaciones de Go finalizan correctamente.
- La documentación permite ejecutar el servicio desde un entorno limpio.
- No se agregan Dockerfiles, manifiestos, frontend ni pipelines.

## 14. Navegación futura del frontend

Cuando se implemente la interfaz web:

- el home mostrará hasta 9 películas por página;
- el detalle tendrá botón `Volver`;
- no habrá edición ni historial porque las películas son datos externos de solo lectura;
- los errores usarán el estilo de llamada a la acción definido por el proyecto.

## 15. Commit sugerido

```text
feat(movies-api): implementar consultas de películas con TMDB
```
