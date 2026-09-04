# Borrador de planificación: Weather API

Status: Draft

Este documento conserva la planificación amplia original. No es una tarea ejecutable y deberá revisarse y dividirse antes del desarrollo. Consulte el [índice del módulo](README.md) y las [convenciones globales](../README.md).

## 1. Objetivo

Desarrollar `apps/weather-api` como un servicio HTTP en Python y FastAPI que consulte un proveedor meteorológico externo y entregue información actual normalizada por ciudad.

El proveedor se seleccionará al comenzar la implementación según disponibilidad, condiciones de uso y soporte para las ubicaciones requeridas. El contrato público del servicio no debe depender de su formato.

## 2. Dependencia previa

Comenzar esta tarea después de completar y validar `movies-api`. Antes de implementar, confirmar el proveedor meteorológico y documentar la decisión sin incorporar credenciales.

## 3. Alcance funcional

Implementar:

- consulta del clima actual por ciudad;
- identificación opcional del país para desambiguar;
- normalización de unidades y condiciones;
- caché local de corta duración;
- endpoints de salud;
- respuestas y errores según las [convenciones globales](../README.md).

No implementar en esta tarea:

- pronóstico extendido;
- historial meteorológico;
- alertas;
- autenticación;
- persistencia;
- frontend;
- Dockerfile;
- manifiestos Kubernetes;
- CI/CD.

## 4. Dependencias y entorno

Utilizar `uv` como administrador de proyecto y dependencias, con un `pyproject.toml` propio para el servicio. El entorno virtual debe quedar dentro de `apps/weather-api/.venv/` y nunca versionarse.

No mezclar `uv`, Poetry y archivos `requirements.txt` administrados manualmente. Si un archivo de requisitos fuese necesario para una herramienta externa, debe generarse desde la fuente de verdad y documentarse.

Estructura conceptual:

```text
apps/weather-api/
├── src/weather_api/
│   ├── api/          # Rutas, dependencias y manejo HTTP
│   ├── core/         # Configuración, errores y logging
│   ├── models/       # Modelos de dominio y DTO
│   ├── services/     # Casos de uso
│   └── providers/    # Adaptador meteorológico
├── tests/
├── .env.example
├── pyproject.toml
├── uv.lock
└── README.md
```

## 5. Contrato inicial

### `GET /health/live`

Confirma que el proceso está activo y responde `200 OK` sin consultar al proveedor.

### `GET /health/ready`

Valida la configuración crítica y el estado interno necesario. No debe consumir innecesariamente la cuota del proveedor.

### `GET /api/v1/weather/current`

Parámetros:

- `city`: texto obligatorio, normalizado y con longitud acotada;
- `country_code`: código de país opcional y validado;
- `units`: `metric` o `imperial`, con valor predeterminado documentado;
- `language`: código opcional dentro de los valores admitidos.

Casos principales:

- `200 OK`: ubicación encontrada;
- `400 Bad Request`: parámetros inválidos;
- `404 Not Found`: ubicación inexistente;
- `429 Too Many Requests`: límite propio o del proveedor;
- `502 Bad Gateway`: respuesta inválida del proveedor;
- `503 Service Unavailable`: proveedor temporalmente no disponible;
- `504 Gateway Timeout`: timeout externo.

## 6. Modelo público del clima actual

Definir un modelo independiente del proveedor que contemple:

- ubicación normalizada;
- país;
- coordenadas cuando estén disponibles;
- fecha y hora de observación;
- temperatura y unidad;
- sensación térmica;
- humedad;
- velocidad y dirección del viento;
- condición meteorológica normalizada;
- fecha de actualización del proveedor.

Los campos ausentes deben manejarse explícitamente; no deben reemplazarse con valores engañosos.

## 7. Integración con el proveedor

- Crear una interfaz o protocolo propio.
- Mantener autenticación, URLs y transformación dentro del adaptador.
- Utilizar un cliente HTTP asíncrono reutilizable con límites y timeout.
- Propagar cancelación cuando el cliente cierre la solicitud.
- Clasificar errores de red, timeout, rate limit, autorización y payload inválido.
- No devolver ni registrar el cuerpo completo de errores externos.
- Confirmar endpoints, unidades y restricciones en la documentación oficial durante la implementación.

Variables mínimas previstas:

```dotenv
APP_ENV=development
HTTP_PORT=8080
WEATHER_PROVIDER=
WEATHER_API_BASE_URL=
WEATHER_API_KEY=
WEATHER_TIMEOUT_SECONDS=5
WEATHER_CACHE_TTL_SECONDS=300
LOG_LEVEL=info
```

## 8. Caché y rendimiento

- Implementar una caché en memoria detrás de una abstracción propia.
- Construir la clave con ciudad normalizada, país, unidades e idioma.
- Configurar el TTL mediante variable de entorno.
- No almacenar errores ni respuestas inválidas.
- Evitar crecimiento ilimitado; establecer un máximo o una política de expulsión simple.
- Preparar la sustitución futura por una caché distribuida sin modificar handlers ni casos de uso.

No introducir Redis en esta tarea.

## 9. Validación y seguridad

- Rechazar ciudades vacías, caracteres de control y longitudes excesivas.
- Normalizar espacios sin alterar nombres válidos.
- Validar códigos, unidades e idiomas mediante listas permitidas.
- Mantener la clave del proveedor exclusivamente en configuración.
- No aceptar CORS irrestricto por defecto.
- Aplicar límites de solicitud y timeouts.
- Incorporar rate limiting desacoplado o dejar preparada su interfaz si requiere infraestructura posterior.

No existen roles ni permisos en esta versión porque el endpoint es una consulta pública de solo lectura. La decisión debe documentarse.

## 10. Historial y auditoría

El servicio no administra entidades mutables, por lo que no tendrá endpoint de historial de cambios.

Registrar únicamente información operativa segura:

- ubicación normalizada sin datos innecesarios;
- resultado de caché;
- duración total y del proveedor;
- código HTTP;
- tipo de error;
- `request_id`.

## 11. Pruebas

Implementar como mínimo:

- pruebas unitarias de normalización y validación;
- pruebas del servicio con proveedor y caché simulados;
- pruebas del adaptador con transporte HTTP simulado;
- pruebas de cache hit, miss, expiración y límite;
- pruebas HTTP de éxito, ubicación inexistente, rate limit, timeout y payload inválido;
- prueba que confirme que una entrada inválida no llama al proveedor;
- prueba que confirme que no se filtran claves ni detalles internos;
- ejecución de la suite con `pytest` y cobertura sobre las capas críticas;
- análisis estático y formato definidos en `pyproject.toml`.

Las pruebas no deben consumir el proveedor real.

## 12. Documentación del módulo

Actualizar `apps/weather-api/README.md` con:

- versión de Python admitida;
- instalación con `uv`;
- variables de entorno;
- ejecución local;
- contrato y ejemplos;
- comportamiento de caché;
- comandos de prueba y calidad;
- errores esperados;
- limitaciones conocidas.

## 13. Criterios de aceptación

- El proyecto posee `pyproject.toml` y lockfile propios.
- El endpoint devuelve un modelo independiente del proveedor.
- La clave externa nunca aparece en código, logs, pruebas ni respuestas.
- La caché respeta su TTL y límite.
- Los errores externos se traducen al contrato compartido.
- Los endpoints de salud no consumen cuota innecesariamente.
- Las pruebas son deterministas y no requieren Internet.
- La documentación permite ejecutar el servicio desde un entorno limpio.
- No se agregan persistencia, Dockerfiles, manifiestos, frontend ni pipelines.

## 14. Navegación futura del frontend

Cuando se implemente la interfaz web:

- el home limitará los resultados o búsquedas recientes a 9 elementos;
- el detalle tendrá botón `Volver`;
- no habrá edición ni historial de cambios;
- los errores externos se presentarán como mensajes accionables y seguros.

## 15. Commit sugerido

```text
feat(weather-api): implementar consulta meteorológica por ciudad
```
