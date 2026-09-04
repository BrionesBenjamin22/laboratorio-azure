# Secure Notes API

Status: Planned

## Propósito

Construir una API stateful en Python y FastAPI para registrar usuarios, autenticar sesiones y administrar notas privadas en PostgreSQL. El módulo introduce persistencia, migraciones, JWT, autorización, concurrencia optimista, auditoría, historial y soft delete como cargas de trabajo para infraestructura posterior.

## Principios

- `uv` como administrador del proyecto y lockfile.
- FastAPI con dependencias explícitas; sin contenedor de DI externo.
- PostgreSQL real para pruebas de integración donde su comportamiento importe.
- Migraciones como única fuente de verdad del esquema.
- Rutas, servicios y persistencia separados cuando exista una responsabilidad real.
- Transacciones delimitadas por caso de uso.
- Contratos HTTP distintos de modelos ORM.
- Secretos y contenido de notas ausentes de logs.
- Ningún administrador accede al contenido privado de notas por defecto.

## Contrato V1 previsto

### Salud

- `GET /health/live`
- `GET /health/ready`

Readiness comprobará configuración, recursos internos y una operación PostgreSQL acotada. Liveness no dependerá de la base.

### Autenticación y usuarios

- `POST /api/v1/auth/register`
- `POST /api/v1/auth/login`
- `POST /api/v1/auth/refresh`
- `POST /api/v1/auth/logout`
- `GET /api/v1/users/me`
- `PATCH /api/v1/users/me`
- `GET /api/v1/users/me/history?page=1`
- `GET /api/v1/admin/users?page=1`
- `PATCH /api/v1/admin/users/{user_id}/status`
- `GET /api/v1/admin/users/{user_id}/history?page=1`

El registro público siempre crea rol `user`. El rol `admin` se aprovisionará mediante un mecanismo offline explícito, nunca mediante un payload público. Un administrador puede consultar metadata de cuentas y activar o desactivar usuarios, pero no leer sus notas.

### Notas

- `POST /api/v1/notes`
- `GET /api/v1/notes?page=1`
- `GET /api/v1/notes/{note_id}`
- `PATCH /api/v1/notes/{note_id}`
- `DELETE /api/v1/notes/{note_id}`
- `GET /api/v1/notes/{note_id}/history?page=1`

Los listados devuelven hasta 9 notas y los historiales 3 eventos por página. No existe `page_size` en V1.

## Modelo conceptual

### Usuario

- UUID, email normalizado, password hash, rol, estado activo, timestamps y versión.
- El hash nunca se devuelve.
- La eliminación de cuentas no forma parte de V1; la desactivación impide nuevas sesiones y renovaciones.

### Sesión

- Identificador, usuario, hash del refresh token, expiración, revocación, timestamps y datos mínimos de auditoría.
- Los refresh tokens se rotan y nunca se almacenan en texto plano.

### Nota

- UUID, propietario, título, contenido, timestamps, `deleted_at` y versión.
- La eliminación es lógica.
- `owner_id` es inmutable y nunca se acepta desde el cliente.

### Historial de nota

- Nota, actor, tipo de evento, diferencias seguras, versión de entidad, fecha y `request_id`.
- Creación, actualización real y soft delete se registran en la misma transacción que el cambio.
- Una edición sin diferencias no escribe ni genera historial.

### Historial de usuario

- Registra creación, cambios reales de perfil, estado y rol cuando corresponda.
- Utiliza diferencias seguras y nunca incluye password hashes, tokens o secretos.
- El usuario consulta su propio historial; el administrador consulta el historial necesario para gestionar cuentas.
- Se pagina con 3 eventos por página.

### Auditoría de seguridad

- Registra registro, login satisfactorio o fallido, renovación, revocación, cambio de estado y acceso denegado.
- Se separa del historial funcional de notas.
- No almacena contraseñas, JWT, refresh tokens ni contenido de notas.

## Autorización

| Actor | Permisos V1 |
| --- | --- |
| Usuario activo | Perfil e historial propios, sesiones propias, notas propias e historial de notas propio. |
| Administrador activo | Permisos de usuario sobre sus datos y administración del estado de cuentas; sin acceso al contenido ajeno. |
| Usuario inactivo | Sin login, refresh ni acceso protegido. |

Para notas ajenas se devolverá una respuesta equivalente a no encontrado, evitando confirmar su existencia.

## Actualizaciones y concurrencia

Los endpoints `PATCH` reciben únicamente campos permitidos y una versión esperada cuando exista riesgo de concurrencia. El backend compara diferencias reales antes de escribir. Una versión obsoleta responde `409 Conflict`.

## Variables previstas

```dotenv
APP_ENV=development
HTTP_HOST=127.0.0.1
HTTP_PORT=8080
DATABASE_URL=
JWT_SECRET=
JWT_ISSUER=secure-notes-api
JWT_AUDIENCE=secure-notes-client
JWT_ACCESS_TTL_MINUTES=15
JWT_REFRESH_TTL_DAYS=7
LOG_LEVEL=INFO
```

Las variables se incorporan cuando una tarea las consume. Parámetros criptográficos y bibliotecas se verificarán contra documentación oficial en TASK-07.

## Mapa de tareas

| Orden | Tarea | Estado |
| --- | --- | --- |
| 1 | [Inicialización Python](TASK-01-python-project-initialization.md) | `Planned` |
| 2 | [Aplicación FastAPI base](TASK-02-base-fastapi-application.md) | `Planned` |
| 3 | [Configuración y lifecycle](TASK-03-configuration-and-lifecycle.md) | `Planned` |
| 4 | [Contrato y middleware HTTP](TASK-04-http-contract-and-middleware.md) | `Planned` |
| 5 | [PostgreSQL, migraciones y readiness](TASK-05-postgresql-and-migrations.md) | `Planned` |
| 6 | [Persistencia de usuarios y auditoría](TASK-06-user-persistence.md) | `Planned` |
| 7 | [Primitivas de autenticación](TASK-07-authentication-primitives.md) | `Planned` |
| 8 | [Registro](TASK-08-user-registration.md) | `Planned` |
| 9 | [Login](TASK-09-login.md) | `Planned` |
| 10 | [Renovación y logout](TASK-10-session-renewal-and-logout.md) | `Planned` |
| 11 | [Perfil propio](TASK-11-current-user-profile.md) | `Planned` |
| 12 | [Administración de usuarios](TASK-12-user-administration.md) | `Planned` |
| 13 | [Persistencia de notas e historial](TASK-13-note-persistence.md) | `Planned` |
| 14 | [Crear nota](TASK-14-create-note.md) | `Planned` |
| 15 | [Listar notas](TASK-15-list-notes.md) | `Planned` |
| 16 | [Detalle de nota](TASK-16-note-detail.md) | `Planned` |
| 17 | [Actualizar nota](TASK-17-update-note.md) | `Planned` |
| 18 | [Eliminar nota](TASK-18-delete-note.md) | `Planned` |
| 19 | [Historial de nota](TASK-19-note-history.md) | `Planned` |
| 20 | [Hardening de autenticación](TASK-20-authentication-hardening.md) | `Planned` |
| 21 | [Hardening transaccional y operativo](TASK-21-operational-hardening.md) | `Planned` |
| 22 | [Validación final y documentación](TASK-22-final-validation-and-documentation.md) | `Planned` |

El orden es lineal. Aunque algunas pruebas podrían escribirse en paralelo, cada tarea debe cerrar y validar su incremento antes de iniciar la siguiente.

## Fuera de alcance

- recuperación de contraseña y correo;
- autenticación social o MFA;
- notas compartidas, adjuntos y búsqueda completa;
- lectura administrativa de notas privadas;
- eliminación pública de cuentas;
- frontend y decisiones UI;
- Dockerfile, Kubernetes, CI/CD y Azure.

## Etapas futuras de infraestructura

Después de validar V1 se planificarán imagen OCI, PostgreSQL local reproducible, Secrets, migraciones de despliegue, volúmenes, backups, probes, políticas de red, escalamiento, observabilidad y rotación de claves. Ninguna se implementa en estas tareas.
