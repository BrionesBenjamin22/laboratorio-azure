# TASK-08 — Caché local

Status: Planned

Prerequisites:

- [TASK-07 — Clima actual](TASK-07-current-weather.md)

Next:

- [TASK-09 — Hardening](TASK-09-hardening.md)

## Contexto

El endpoint funcional llama al proveedor en cada request. Una caché local pequeña permite reducir cuota y estudiar el comportamiento stateless por réplica sin introducir infraestructura distribuida.

## Objetivo

Agregar una caché en memoria con TTL, capacidad máxima y comportamiento concurrente seguro.

## Alcance

- Definir la responsabilidad de caché cerca del servicio consumidor.
- Construir claves con ciudad y país normalizados más configuración que cambie el resultado.
- Configurar TTL y capacidad máxima.
- No almacenar errores ni respuestas inválidas.
- Resolver accesos concurrentes correctamente en el modelo async.
- Exponer cache hit o miss únicamente en logging seguro.
- Probar hit, miss, expiración, capacidad y claves.

## Fuera de alcance

- Redis o caché compartida entre réplicas.
- Invalidación distribuida.
- Persistencia y precarga.
- Garantía global de una sola llamada concurrente si requiere complejidad injustificada.

## Archivos probablemente afectados

- Servicio y componente de caché.
- Configuración y `.env.example`.
- Lifespan si necesita limpieza.
- Tests con reloj controlable.

## Pasos esperados

1. Definir semántica y reloj inyectable mínimo.
2. Implementar almacenamiento acotado.
3. Integrarlo sin modificar el handler.
4. Probar expiración sin esperas reales.
5. Documentar que cada proceso tiene caché independiente.

## Restricciones

- No usar sleeps en tests.
- No almacenar secretos ni errores.
- No introducir backend distribuido o interfaz genérica multipropósito.
- Mantener resultados inmutables o protegidos frente a mutación accidental.

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

- Hits válidos evitan una nueva llamada externa.
- Entradas vencidas vuelven a consultarse.
- La capacidad no crece indefinidamente.
- Errores nunca quedan cacheados.
- Los tests controlan el tiempo de forma determinista.
- Se documenta el alcance por proceso y réplica.

## Resultado esperado

Caché local predecible que reduce llamadas sin cambiar el contrato HTTP.

## Siguiente tarea

Detenerse e informar resultados. Continuar con [TASK-09](TASK-09-hardening.md).
