# TASK-09 — Hardening

Status: Planned

Prerequisites:

- [TASK-08 — Detalle de película](TASK-08-movie-detail.md)

Next:

- [TASK-10 — Suite final y documentación](TASK-10-final-validation-and-documentation.md)

## Contexto

El contrato V1 está completo. Antes de cerrar el módulo se necesita una revisión transversal de seguridad y operación, sin sumar funcionalidades futuras.

## Objetivo

Auditar y corregir configuración, timeouts, cancelación, validaciones, secretos, logging y shutdown con cambios concretos y justificados.

## Alcance

- Revisar timeouts del servidor y cliente TMDB.
- Confirmar propagación de `context` y cancelación.
- Revisar validaciones de `page`, `query` y `movie_id`.
- Comprobar que token y `Authorization` no aparecen en logs, errores o fixtures.
- Revisar tamaño máximo de headers y de respuestas externas solo si existe un riesgo concreto.
- Verificar cierre de cuerpos HTTP y conexiones reutilizables.
- Probar shutdown con solicitudes activas y timeout.
- Confirmar que readiness no depende de TMDB.
- Confirmar que CORS y rate limiting siguen ausentes y documentar dónde se evaluarán.

## Fuera de alcance

- Nuevos endpoints o campos funcionales.
- CORS anticipado.
- Abstracción o implementación de rate limiting.
- headers orientados a HTML sin efecto concreto para esta API.
- retries, circuit breaker, caché u OpenTelemetry.
- Docker y Kubernetes.

## Archivos probablemente afectados

- Configuración, arranque, servidor HTTP y adaptador TMDB.
- Tests de integración interna.
- `.env.example` y documentación si se detectan discrepancias.

La tarea puede concluir sin modificar una zona que ya cumpla los criterios.

## Pasos esperados

1. Inventariar timeouts, límites y rutas de logs.
2. Seguir una solicitud desde handler hasta TMDB y de regreso.
3. Revisar cada error público y su status.
4. Simular cancelación, timeout externo y shutdown.
5. Buscar material sensible en archivos y salida de pruebas.
6. Corregir únicamente hallazgos reproducibles.
7. Documentar riesgos aceptados y decisiones postergadas.

## Restricciones

- No convertir el hardening en una fase de funcionalidades.
- No agregar infraestructura por previsión.
- No incorporar dependencias sin demostrar que la biblioteca estándar no resuelve el hallazgo.
- Mantener mensajes seguros y accionables.

## Validaciones

```powershell
Set-Location apps/movies-api
gofmt -w .
go test ./...
go vet ./...
go test -race ./...
```

```bash
cd apps/movies-api
gofmt -w .
go test ./...
go vet ./...
go test -race ./...
```

`go test -race ./...` es recomendado. Si el entorno no lo soporta, registrar la limitación y no ocultarla.

## Criterios de aceptación

- Todos los timeouts tienen propósito y configuración claros.
- Cancelación y shutdown están cubiertos por pruebas razonables.
- Readiness continúa independiente de TMDB.
- No se detectan secretos ni headers de autorización en logs o respuestas.
- Los parámetros inválidos fallan antes de invocar dependencias.
- No existen CORS, rate limiting u observabilidad avanzada prematuros.
- Cada corrección corresponde a un hallazgo verificable.

## Resultado esperado

Una V1 endurecida dentro de su alcance, con riesgos postergados documentados.

## Siguiente tarea

Detenerse e informar resultados. Continuar después con [TASK-10](TASK-10-final-validation-and-documentation.md).
