# Tarea 03: Secure Notes API

## 1. Objetivo

Desarrollar `apps/secure-notes-api` como un servicio HTTP en Python y FastAPI para registrar usuarios, autenticar sesiones mediante JWT y administrar notas privadas persistidas en PostgreSQL.

Este módulo introduce persistencia, migraciones, permisos, auditoría, historial de cambios, soft delete y protección de endpoints críticos.

## 2. Dependencias previas

Comenzar después de validar `movies-api` y `weather-api`. Antes de escribir endpoints, definir y documentar:

- versión de PostgreSQL;
- estrategia de migraciones;
- duración y rotación de tokens;
- roles iniciales;
- política de contraseñas;
- política de eliminación y conservación del historial.

## 3. Alcance funcional

Implementar:

- registro de usuarios;
- inicio de sesión;
- renovación y revocación de sesión;
- consulta y edición del perfil propio;
- creación, listado, detalle, edición y eliminación lógica de notas;
- historial paginado de cambios de cada nota;
- auditoría de eventos de seguridad;
- permisos por propietario y rol;
- endpoints de salud;
- respuestas según [`docs/README.md`](README.md).

No implementar en esta tarea:

- recuperación de contraseña por correo;
- autenticación social;
- notas compartidas entre usuarios;
- archivos adjuntos;
- búsqueda de texto completo;
- frontend;
- Dockerfile;
- manifiestos Kubernetes;
- CI/CD;
- despliegue en Azure.

## 4. Dependencias y estructura

Utilizar `uv` con un `pyproject.toml` y un lockfile propios. Utilizar una herramienta de migraciones compatible con el ORM elegido y mantener una única fuente de verdad para dependencias.

Estructura conceptual:

```text
apps/secure-notes-api/
├── src/secure_notes_api/
│   ├── api/             # Rutas, dependencias y controllers
│   ├── auth/            # Tokens, contraseñas y permisos
│   ├── core/            # Configuración, errores y logging
│   ├── models/          # Modelos de dominio y persistencia
│   ├── repositories/    # Acceso a datos
│   ├── schemas/         # Contratos HTTP
│   └── services/        # Casos de uso
├── migrations/
├── tests/
├── .env.example
├── pyproject.toml
├── uv.lock
└── README.md
```

Las rutas no deben consultar directamente al ORM. Las transacciones deben delimitarse en servicios o unidades de trabajo.

## 5. Modelo de datos inicial

### Usuario

Campos mínimos:

- `id`: UUID;
- `email`: único y normalizado;
- `password_hash`;
- `role`: `user` o `admin`;
- `is_active`;
- `created_at`;
- `updated_at`;
- `deleted_at` opcional;
- `version`.

Nunca persistir contraseñas en texto plano ni devolver `password_hash`.

### Nota

Campos mínimos:

- `id`: UUID;
- `owner_id`;
- `title`;
- `content`;
- `created_at`;
- `updated_at`;
- `deleted_at` opcional;
- `version`.

### Historial de nota

Campos mínimos:

- `id`;
- `note_id`;
- `actor_id`;
- `event_type`;
- diferencias de campos seguras;
- `entity_version`;
- `created_at`;
- `request_id`.

El historial debe registrar creación, cambios reales, eliminación lógica y restauración si se incorpora. No debe registrar una actualización cuando el payload no modifica valores.

### Sesión o token de renovación

Persistir únicamente información que permita rotación y revocación segura. No almacenar tokens reutilizables en texto plano.

### Auditoría de seguridad

Separar la auditoría de autenticación del historial funcional de notas. Registrar eventos como login satisfactorio o fallido, renovación, revocación y denegación de acceso sin almacenar contraseñas, JWT ni contenido de notas.

## 6. Contrato de autenticación

### `POST /api/v1/auth/register`

- Valida y normaliza el correo.
- Aplica la política de contraseña.
- Crea usuarios con rol `user`; el cliente no puede elegir un rol administrativo.
- Responde `201 Created` con un mensaje de éxito visible.
- Una colisión de correo utiliza `409 Conflict` sin revelar información adicional innecesaria.

### `POST /api/v1/auth/login`

- Acepta correo y contraseña.
- Utiliza un mensaje genérico para credenciales inválidas.
- Aplica protección ante intentos repetidos.
- Devuelve access token y mecanismo de renovación según la estrategia seleccionada.

### `POST /api/v1/auth/refresh`

- Rota el token de renovación.
- Detecta reutilización cuando la estrategia elegida lo permita.
- Rechaza sesiones vencidas, revocadas o pertenecientes a usuarios inactivos.

### `POST /api/v1/auth/logout`

- Revoca la sesión actual de forma idempotente.

## 7. Contrato de usuarios

### `GET /api/v1/users/me`

Devuelve únicamente los datos seguros del usuario autenticado.

### `PATCH /api/v1/users/me`

- Acepta actualizaciones parciales.
- Aplica únicamente diferencias reales.
- Si no existen cambios, no ejecuta una escritura ni genera historial.
- Verifica unicidad al modificar el correo.

La administración global de usuarios queda fuera del alcance inicial, aunque la arquitectura debe permitir incorporarla sin mezclarla con las rutas del perfil propio.

## 8. Contrato de notas

### `POST /api/v1/notes`

- Crea una nota para el usuario autenticado.
- Ignora o rechaza cualquier `owner_id` enviado por el cliente.
- Responde `201 Created` y un mensaje de éxito.

### `GET /api/v1/notes`

- Devuelve únicamente notas activas del propietario.
- Utiliza `page` mayor o igual que 1.
- Limita `page_size` a un máximo de 9 y utiliza 9 por defecto.
- Permite ordenamiento mediante una lista segura de campos.

### `GET /api/v1/notes/{note_id}`

- Devuelve la nota únicamente a su propietario.
- Evita revelar si una nota ajena existe; utilizar una respuesta equivalente a no encontrado.

### `PATCH /api/v1/notes/{note_id}`

- Acepta únicamente `title`, `content` y la versión esperada.
- Envía y persiste solo diferencias reales.
- No escribe ni genera historial cuando no hay cambios.
- Detecta actualizaciones concurrentes mediante `version` y responde `409 Conflict`.

### `DELETE /api/v1/notes/{note_id}`

- Realiza soft delete.
- Es idempotente desde la perspectiva del cliente.
- Registra el evento en el historial.

### `GET /api/v1/notes/{note_id}/history`

- Solo puede consultarlo el propietario de la nota.
- Devuelve cambios ordenados del más reciente al más antiguo.
- Utiliza 3 elementos por página.
- No expone hashes, tokens ni valores que la política de seguridad determine sensibles.

## 9. Permisos

### Usuario activo

- Administrar su perfil permitido.
- Crear y administrar sus propias notas.
- Consultar el historial de sus propias notas.

### Administrador

- Disponer de un punto de extensión para operaciones administrativas futuras.
- No acceder al contenido privado de notas por defecto.
- No utilizar endpoints de usuario para alterar roles.

### Usuario inactivo o eliminado

- No iniciar ni renovar sesiones.
- No acceder a endpoints protegidos.

La autorización se validará en backend para cada recurso. Las comprobaciones frontend futuras serán únicamente preventivas y nunca sustituirán estos controles.

## 10. Contraseñas y JWT

- Utilizar un algoritmo de hashing de contraseñas vigente y configurable, con salt gestionado por la biblioteca.
- Definir longitud mínima y máxima para evitar entradas débiles o abusivas.
- Aplicar comparación segura y mensajes genéricos.
- Incluir en JWT solo claims necesarios: sujeto, rol, emisión, expiración e identificador del token.
- Validar firma, algoritmo, emisor, audiencia y expiración.
- Utilizar secretos distintos por ambiente y permitir rotación.
- Mantener access tokens de duración breve.
- Revocar y rotar tokens de renovación.

La selección final de bibliotecas y parámetros criptográficos deberá verificarse con documentación oficial durante la implementación.

## 11. Configuración

Variables mínimas previstas:

```dotenv
APP_ENV=development
HTTP_PORT=8080
DATABASE_URL=
JWT_SECRET=
JWT_ISSUER=secure-notes-api
JWT_AUDIENCE=secure-notes-client
JWT_ACCESS_TTL_MINUTES=15
JWT_REFRESH_TTL_DAYS=7
LOG_LEVEL=info
```

El `.env.example` debe utilizar valores ficticios y explicar cómo proveer secretos reales fuera de Git.

## 12. Persistencia y migraciones

- Gestionar el esquema exclusivamente mediante migraciones versionadas.
- Incluir restricciones, índices y claves foráneas en la base.
- Indexar correo normalizado, propietario de nota, estado y campos de ordenamiento frecuentes.
- Evitar consultas N+1.
- Paginar en la base de datos; no cargar todas las notas o historiales en memoria.
- Mantener transacciones atómicas entre cambios de nota e historial.
- No borrar físicamente notas dentro del flujo normal.
- Definir una estrategia explícita para datos de usuarios eliminados.

## 13. Errores y mensajes

Utilizar códigos estables para validación, autenticación, autorización, conflicto de versión, recurso no encontrado y dependencia no disponible.

Los mensajes públicos deben ser seguros y accionables. Ejemplo para una dependencia no disponible:

```text
Lo sentimos. No pudimos recuperar la información. Intente nuevamente.
```

Los mensajes de éxito deben identificar la operación sin incluir datos privados.

## 14. Salud y observabilidad

### `GET /health/live`

Comprueba únicamente que el proceso está activo.

### `GET /health/ready`

Comprueba configuración y conectividad con PostgreSQL mediante una operación acotada por timeout.

Los logs deben incluir `request_id`, usuario cuando sea seguro, operación, resultado y duración. No deben incluir contraseñas, tokens ni contenido de notas.

## 15. Pruebas

Implementar como mínimo:

- pruebas unitarias de servicios, permisos y validaciones;
- pruebas del hashing y validación de tokens sin registrar secretos;
- pruebas de registro, login, refresh, logout y usuarios inactivos;
- pruebas de aislamiento entre propietarios;
- pruebas CRUD y soft delete de notas;
- pruebas de actualización sin diferencias;
- pruebas de conflicto por versión;
- pruebas del historial y paginación de 3 elementos;
- pruebas de listados con máximo de 9 elementos;
- pruebas de transacciones y rollback;
- pruebas de migración sobre PostgreSQL aislado;
- pruebas de handlers y contrato de errores;
- análisis estático, formato y cobertura de capas críticas.

No sustituir PostgreSQL por SQLite en las pruebas que validen comportamiento de persistencia específico.

## 16. Desarrollo local e infraestructura

La aplicación debe poder ejecutarse con variables de entorno y una instancia local de PostgreSQL. Si se necesita agregar Docker Compose para la base de datos, deberá proponerse como un cambio raíz separado porque afecta infraestructura compartida.

Esta tarea no debe crear manifiestos Kubernetes. Sí debe dejar preparado:

- inicio sin estado local salvo PostgreSQL;
- migraciones ejecutables de forma controlada;
- cierre ordenado;
- configuración externa;
- healthchecks compatibles con contenedores;
- ejecución detrás de proxy reverso.

## 17. Documentación del módulo

Actualizar `apps/secure-notes-api/README.md` con:

- requisitos y puesta en marcha;
- configuración y secretos;
- migraciones;
- endpoints y permisos;
- modelos y relaciones;
- estados y soft delete;
- historial y auditoría;
- comandos de prueba;
- decisiones de seguridad;
- limitaciones conocidas.

## 18. Criterios de aceptación

- Registro y autenticación funcionan sin exponer información sensible.
- Los roles no pueden asignarse desde payloads públicos.
- Cada usuario solo accede a sus notas e historial.
- Los listados tienen un máximo de 9 elementos y los historiales 3.
- Las ediciones persisten únicamente diferencias reales.
- Las escrituras y su historial son atómicos.
- Las notas se eliminan mediante soft delete.
- Las sesiones pueden renovarse y revocarse de forma segura.
- Las migraciones crean el esquema completo desde una base vacía.
- Los errores siguen el contrato compartido y contienen `request_id`.
- La suite completa finaliza correctamente sobre PostgreSQL.
- La documentación permite reproducir el entorno sin secretos versionados.

## 19. Requisitos del frontend futuro

Cuando se implemente la interfaz web:

- el alta de una nota volverá al home con `successMessage`;
- la edición volverá al detalle con `successMessage`;
- el home mostrará 9 notas por página;
- el detalle incluirá tarjeta principal, `Auditoría`, `Historial de cambios`, `Volver` y `Editar` cuando corresponda;
- el historial mostrará 3 elementos por página;
- la edición enviará únicamente campos modificados;
- si no hay cambios, no llamará al backend;
- los errores mostrarán el mensaje seguro provisto por la API;
- los controles visibles respetarán estado y permisos, sin reemplazar la autorización backend.

## 20. Commit sugerido

```text
feat(secure-notes-api): implementar autenticación y gestión de notas privadas
```
