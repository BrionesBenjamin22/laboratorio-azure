# TASK-03 — Configuración y lifecycle

Status: Planned

Prerequisites:

- [TASK-02 — Servidor HTTP base](TASK-02-base-http-server.md)

Next:

- [TASK-04 — Contrato y middleware HTTP](TASK-04-http-contract-and-middleware.md)

## Contexto

El servidor base utiliza valores iniciales, pero todavía no valida configuración, no expresa readiness ni responde ordenadamente ante terminación.

## Objetivo

Introducir configuración tipada, logging base con `log/slog`, `GET /health/ready` y graceful shutdown preparado para el lifecycle futuro de Kubernetes.

## Alcance

- Leer configuración desde variables de entorno con biblioteca estándar.
- Validar puerto, timeouts del servidor y timeout de shutdown.
- Incorporar `log/slog` con nivel configurable si puede hacerse sin complejidad innecesaria.
- Implementar `/health/ready` basado exclusivamente en configuración e inicialización internas.
- Manejar señales de terminación mediante contexto.
- Ejecutar `http.Server.Shutdown` con plazo limitado.
- Evitar aceptar indefinidamente nuevas solicitudes durante el cierre.
- Documentar la relación conceptual Kubernetes → `SIGTERM` → aplicación → graceful shutdown.

## Fuera de alcance

- Consultar TMDB desde readiness.
- Configurar todavía el token de TMDB si el adaptador no existe.
- Middleware de logging por request.
- Contrato final de errores.
- CORS, rate limiting u observabilidad avanzada.

## Archivos probablemente afectados

- Punto de entrada del servicio.
- Paquete de configuración cuando su responsabilidad lo justifique.
- Handlers de health.
- Pruebas de configuración y lifecycle.
- `.env.example` para las variables incorporadas.

## Pasos esperados

1. Identificar valores actualmente embebidos que deban configurarse.
2. Crear una estructura de configuración pequeña y validarla al inicio.
3. Inicializar el logger y el servidor a partir de esa configuración.
4. Añadir readiness sin dependencia de red.
5. conectar señales a un contexto de cancelación.
6. Aplicar shutdown con timeout y diferenciar cierre normal de error de servidor.
7. Probar configuración válida e inválida y el estado de readiness.

## Restricciones

- Priorizar `os`, `strconv`, `time`, `context`, `os/signal`, `syscall` y `log/slog`.
- No agregar una biblioteca `.env`; el archivo `.env.example` es documentación, no un cargador.
- No crear `internal/platform` ni un paquete genérico de utilidades.
- Readiness no puede depender de la disponibilidad instantánea de TMDB.
- No registrar valores secretos.

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

La prueba manual debe comprobar `live`, `ready`, configuración inválida y terminación ordenada con la señal disponible en el sistema.

## Criterios de aceptación

- La configuración se valida antes de servir tráfico.
- `live` sigue siendo independiente de configuración externa.
- `ready` no realiza llamadas a TMDB.
- El proceso registra inicio y cierre mediante `slog`.
- El servidor completa o cancela solicitudes activas dentro de un plazo definido.
- No se filtran secretos en errores o logs.
- Pruebas y análisis estático finalizan correctamente.

## Resultado esperado

Un servidor configurable y con lifecycle operativo predecible, todavía sin integración externa.

## Siguiente tarea

Detenerse e informar resultados. Continuar después con [TASK-04](TASK-04-http-contract-and-middleware.md).
