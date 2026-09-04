# TASK-07 — Clima actual

Status: Planned

Prerequisites:

- [TASK-06 — Adaptador meteorológico](TASK-06-weather-provider-adapter.md)

Next:

- [TASK-08 — Caché local](TASK-08-local-cache.md)

## Contexto

El adaptador existe, pero todavía no hay caso de uso ni endpoint público.

## Objetivo

Implementar `GET /api/v1/weather/current` con validación, servicio y contrato normalizado.

## Alcance

- Definir cerca del servicio un protocolo mínimo del proveedor.
- Validar `city` obligatoria y `country_code` opcional.
- Normalizar espacios y código de país sin alterar nombres válidos.
- Rechazar vacío, controles y longitudes excesivas antes de llamar al proveedor.
- Mapear el modelo interno al envelope público.
- Traducir errores externos al contrato HTTP.
- Probar servicio y endpoint con fake o transporte simulado.

## Fuera de alcance

- Parámetros de idioma o unidades.
- Geocodificación avanzada, sugerencias o múltiples coincidencias propias.
- Caché y persistencia.
- Pronóstico.

## Archivos probablemente afectados

- Servicio, protocolo consumidor y modelos.
- Router y handler.
- Adaptador solo para satisfacer estructuralmente el protocolo.
- Tests unitarios y HTTP.

## Pasos esperados

1. Definir contrato consumidor mínimo.
2. Implementar validación proporcional.
3. Crear caso de uso y handler.
4. Conectar adaptador mediante dependencias de FastAPI sin contenedor externo.
5. Probar éxito, validación y todos los errores mapeados.

## Restricciones

- No crear un framework propio de DI.
- No usar regex que rechace nombres internacionales válidos.
- No llamar al proveedor ante parámetros inválidos.
- No exponer detalles del proveedor.

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

- El endpoint devuelve el modelo V1 documentado.
- Ciudad inválida responde `400` sin invocar proveedor.
- Ubicación inexistente responde `404`.
- Timeout, cuota e indisponibilidad tienen estados seguros y distinguibles.
- Idioma y unidades provienen de configuración.
- No existe todavía caché o funcionalidad adicional.

## Resultado esperado

Primer y único flujo funcional V1 completo.

## Siguiente tarea

Detenerse e informar resultados. Continuar con [TASK-08](TASK-08-local-cache.md).
