# TASK-06 — Adaptador meteorológico

Status: Planned

Prerequisites:

- [TASK-05 — Selección del proveedor](TASK-05-provider-selection.md)

Next:

- [TASK-07 — Clima actual](TASK-07-current-weather.md)

## Contexto

El proveedor y el contrato interno ya están definidos. Se necesita un adaptador asíncrono comprobable antes de exponerlo por HTTP.

## Objetivo

Implementar el cliente del proveedor con autenticación, timeout, transformación y tests sin Internet.

## Alcance

- Agregar el cliente HTTP asíncrono elegido y bloquear su versión.
- Crear una instancia reutilizable administrada por lifespan.
- Implementar consulta por ciudad y país opcional.
- Aplicar idioma y unidades desde configuración.
- Propagar timeout y cancelación.
- Mapear no encontrado, autorización, rate limit, timeout, indisponibilidad y payload inválido.
- Probar con transporte simulado.

## Fuera de alcance

- Endpoint público.
- Caché, retries, circuit breaker o múltiples proveedores.
- Requests reales en tests.

## Archivos probablemente afectados

- Dependencias y lockfile.
- Configuración y `.env.example`.
- Adaptador y DTO externos.
- Lifespan y tests del adaptador.

## Pasos esperados

1. Incorporar la dependencia mínima.
2. Crear el cliente reutilizable.
3. Construir request y autenticación según documentación oficial.
4. Transformar solo campos V1.
5. Traducir errores.
6. Cerrar el cliente durante shutdown.
7. Probar URL, parámetros, timeout y payloads.

## Restricciones

- No exponer DTO externos.
- No registrar clave, headers sensibles ni cuerpos completos.
- No crear una interfaz del cliente HTTP si el transporte simulado resuelve pruebas.
- No consultar salud del proveedor desde readiness.

## Validaciones

```powershell
Set-Location apps/weather-api
uv lock --check
uv run pytest
uv run ruff check .
uv run ruff format --check .
```

```bash
cd apps/weather-api
uv lock --check
uv run pytest
uv run ruff check .
uv run ruff format --check .
```

## Criterios de aceptación

- El cliente es reutilizable y se cierra por lifespan.
- Configuración y autenticación son externas al código.
- Los errores mínimos pueden distinguirse sin filtrar respuestas externas.
- Los tests son deterministas y no usan Internet.
- Readiness continúa sin llamar al proveedor.

## Resultado esperado

Adaptador externo listo para ser consumido por el caso de uso.

## Siguiente tarea

Detenerse e informar resultados. Continuar con [TASK-07](TASK-07-current-weather.md).
