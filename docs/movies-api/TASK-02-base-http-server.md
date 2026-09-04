# TASK-02 — Servidor HTTP base

Status: Planned

Prerequisites:

- [TASK-01 — Inicialización del módulo Go](TASK-01-go-module-initialization.md)

Next:

- [TASK-03 — Configuración y lifecycle](TASK-03-configuration-and-lifecycle.md)

## Contexto

El módulo Go existe, pero todavía no tiene proceso ejecutable. Este incremento introduce únicamente el servidor HTTP y una prueba de vida usando la biblioteca estándar.

## Objetivo

Crear un servidor mínimo con `net/http`, timeouts básicos y `GET /health/live`.

## Alcance

- Crear el punto de entrada ejecutable.
- Construir el router con `http.ServeMux`.
- Implementar `GET /health/live` sin dependencias externas.
- Configurar timeouts básicos del `http.Server` con valores iniciales explícitos.
- Probar el handler con `httptest`.

## Fuera de alcance

- Configuración completa por variables de entorno.
- `/health/ready`.
- graceful shutdown y señales.
- middleware, `request_id` o contrato final de errores.
- TMDB y endpoints de películas.
- frameworks HTTP externos.

## Archivos probablemente afectados

- `apps/movies-api/cmd/api/main.go`.
- Uno o más archivos pequeños para router y health handler si separar esas responsabilidades mejora las pruebas.
- Archivos de prueba correspondientes.

No crear el árbol completo propuesto en la especificación del módulo.

## Pasos esperados

1. Revisar el módulo existente.
2. Elegir la estructura mínima que permita probar el handler sin iniciar un puerto real.
3. Registrar únicamente la ruta `GET /health/live`.
4. Responder JSON pequeño y estado `200 OK`.
5. Configurar `ReadHeaderTimeout`, `ReadTimeout`, `WriteTimeout` e `IdleTimeout` con valores iniciales razonables.
6. Agregar pruebas para método y respuesta.

## Restricciones

- Usar `net/http`, `encoding/json` y `httptest`.
- No agregar router, framework de validación ni inyección de dependencias externos.
- `live` no debe consultar configuración, red ni TMDB.
- Evitar paquetes cuyo único propósito sea contener utilidades genéricas.

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

Realizar además una prueba manual de `GET /health/live` y confirmar que otros métodos reciben una respuesta controlada.

## Criterios de aceptación

- El servicio inicia mediante un único entrypoint claro.
- `GET /health/live` devuelve `200 OK` sin dependencias.
- El handler es comprobable con `httptest`.
- El servidor posee timeouts explícitos.
- No se agregaron dependencias externas ni paquetes prematuros.
- `go test ./...` y `go vet ./...` finalizan correctamente.

## Resultado esperado

Un proceso HTTP mínimo que demuestra liveness y permite evolucionar configuración y lifecycle.

## Siguiente tarea

Detenerse e informar resultados. Continuar después con [TASK-03](TASK-03-configuration-and-lifecycle.md).
