# TASK-04 — Contrato y middleware HTTP

Status: Planned

Prerequisites:

- [TASK-03 — Configuración y lifecycle](TASK-03-configuration-and-lifecycle.md)

Next:

- [TASK-05 — Adaptador TMDB](TASK-05-tmdb-adapter.md)

## Contexto

Los endpoints funcionales necesitan un contrato consistente antes de incorporarse. Resolverlo ahora evita que cada handler invente su propio formato de error, identificación y logging.

## Objetivo

Definir helpers HTTP mínimos y middleware para `request_id`, logging estructurado y errores públicos uniformes.

## Alcance

- Generar o propagar de forma controlada un `request_id`.
- Incluirlo en el contexto, header de respuesta, logs y errores públicos.
- Registrar método, ruta, estado y `duration_ms`.
- Definir un formato JSON mínimo para errores.
- Mapear errores conocidos a estados HTTP sin jerarquías complejas.
- Garantizar `Content-Type: application/json` en respuestas JSON.
- Evaluar explícitamente si hace falta recuperación de panic adicional al comportamiento de `net/http`; documentar la decisión.

## Fuera de alcance

- Errores específicos de TMDB que todavía no existen.
- Métricas, tracing u OpenTelemetry.
- CORS.
- rate limiting.
- autenticación.
- headers orientados principalmente a documentos HTML.

## Archivos probablemente afectados

- Capa HTTP o paquete `httpapi` existente.
- Middleware y helpers de respuesta.
- Pruebas HTTP.

No se exige un archivo por helper; mantener una organización proporcional al tamaño real.

## Pasos esperados

1. Revisar cómo responden los handlers actuales.
2. Definir la estructura pública mínima de error.
3. Implementar identificación de requests.
4. Capturar estado sin romper interfaces opcionales de `http.ResponseWriter` que el servicio realmente utilice.
5. Incorporar logging HTTP con `slog`.
6. Migrar health handlers solo donde mejore consistencia sin acoplar su semántica.
7. Probar headers, JSON, estado, duración y ausencia de secretos.

## Restricciones

- Utilizar biblioteca estándar.
- Evitar una taxonomía extensa de errores.
- No confiar ciegamente en identificadores enviados por clientes; validarlos o generar uno nuevo.
- No registrar query sensible, headers completos ni cuerpos.
- No agregar middleware para necesidades futuras inexistentes.

## Validaciones

```powershell
Set-Location apps/movies-api
gofmt -w .
go test ./...
go vet ./...
```

```bash
cd apps/movies-api
gofmt -w .
go test ./...
go vet ./...
```

## Criterios de aceptación

- Toda respuesta de error HTTP usa un formato estable.
- Los errores incluyen un `request_id` correlacionable con logs.
- Cada request produce un log estructurado con los cinco campos acordados.
- Los handlers no conocen detalles del logger global.
- No se incorporaron CORS, rate limiting ni observabilidad avanzada.
- Las pruebas cubren éxito, error y comportamiento del middleware.

## Resultado esperado

Una frontera HTTP consistente lista para recibir los casos de uso de películas.

## Siguiente tarea

Detenerse e informar resultados. Continuar después con [TASK-05](TASK-05-tmdb-adapter.md).
