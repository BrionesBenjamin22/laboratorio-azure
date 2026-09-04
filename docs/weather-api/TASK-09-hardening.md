# TASK-09 — Hardening

Status: Planned

Prerequisites:

- [TASK-08 — Caché local](TASK-08-local-cache.md)

Next:

- [TASK-10 — Validación final y documentación](TASK-10-final-validation-and-documentation.md)

## Contexto

El flujo y la caché están completos. Se necesita revisar seguridad y operación sin incorporar capacidades futuras.

## Objetivo

Auditar timeouts, lifecycle, validación, secretos, logging, concurrencia y errores del módulo.

## Alcance

- Seguir una request completa hasta proveedor o caché.
- Revisar timeouts y cancelación.
- Confirmar cierre del cliente durante shutdown.
- Revisar concurrencia y límite de caché.
- Buscar filtraciones de claves, headers o payloads.
- Confirmar que readiness no depende de red externa.
- Revisar que errores públicos sean seguros.
- Documentar decisiones postergadas de CORS y rate limiting.

## Fuera de alcance

- Nuevos endpoints o campos.
- retries, circuit breaker, Redis u observabilidad avanzada.
- CORS y rate limiting anticipados.
- Docker o Kubernetes.

## Archivos probablemente afectados

- Configuración, provider, caché, HTTP y tests.
- Documentación únicamente cuando refleje un hallazgo.

## Pasos esperados

1. Inventariar límites y datos registrados.
2. Simular timeout, cancelación, cuota y shutdown.
3. Ejecutar pruebas concurrentes pertinentes.
4. Corregir solo hallazgos reproducibles.
5. Registrar riesgos aceptados.

## Restricciones

- No convertir hardening en desarrollo funcional.
- No agregar infraestructura preventiva.
- No sumar dependencias sin una limitación demostrada.

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

- Timeouts y lifecycle tienen pruebas.
- No se filtran secretos.
- La caché permanece acotada y segura bajo concurrencia.
- Readiness no consulta al proveedor.
- No se incorporaron CORS, rate limiting o infraestructura futura.

## Resultado esperado

Weather API endurecida dentro del alcance V1.

## Siguiente tarea

Detenerse e informar resultados. Continuar con [TASK-10](TASK-10-final-validation-and-documentation.md).
