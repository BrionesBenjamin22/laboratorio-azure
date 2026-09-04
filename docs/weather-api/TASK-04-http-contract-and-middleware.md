# TASK-04 — Contrato y middleware HTTP

Status: Planned

Prerequisites:

- [TASK-03 — Configuración y lifecycle](TASK-03-configuration-and-lifecycle.md)

Next:

- [TASK-05 — Selección del proveedor](TASK-05-provider-selection.md)

## Contexto

Antes del endpoint funcional se necesita un límite HTTP uniforme para errores, identificación y logs.

## Objetivo

Consolidar errores públicos, `request_id` y logging HTTP aprovechando FastAPI sin reemplazar innecesariamente sus mecanismos.

## Alcance

- Definir envelope mínimo de error.
- Mapear errores de validación a mensajes seguros.
- Generar o validar `request_id` y devolverlo en header y errores.
- Registrar método, ruta, status y `duration_ms`.
- Incorporar handlers de excepción concretos.
- Probar que trazas y detalles internos no se exponen.

## Fuera de alcance

- Errores del proveedor todavía inexistentes.
- Middleware de CORS, rate limiting, métricas o tracing.
- Framework externo de errores.

## Archivos probablemente afectados

- Frontera HTTP, middleware y excepciones.
- Configuración de logging.
- Tests HTTP.

## Pasos esperados

1. Definir códigos públicos pequeños y estables.
2. Integrar validación de FastAPI al envelope.
3. Añadir correlación y logging.
4. Probar errores inesperados en modo no debug.
5. Confirmar que no se registran headers o cuerpos completos.

## Restricciones

- No duplicar funcionalidades de FastAPI sin necesidad.
- No construir una jerarquía extensa de excepciones.
- No confiar sin validación en IDs del cliente.
- No habilitar debug en ambientes no locales.

## Validaciones

```powershell
Set-Location apps/weather-api
uv run pytest
uv run ruff check .
uv run ruff format --check .
```

```bash
cd apps/weather-api
uv run pytest
uv run ruff check .
uv run ruff format --check .
```

## Criterios de aceptación

- Errores HTTP y de validación usan un contrato uniforme.
- Cada respuesta permite correlación mediante `request_id`.
- Cada request genera logging mínimo y seguro.
- No existen CORS, rate limiting ni observabilidad avanzada.

## Resultado esperado

Frontera HTTP consistente antes de integrar el proveedor.

## Siguiente tarea

Detenerse e informar resultados. Continuar con [TASK-05](TASK-05-provider-selection.md).
