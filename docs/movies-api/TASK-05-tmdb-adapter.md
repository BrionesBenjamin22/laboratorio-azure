# TASK-05 — Adaptador TMDB

Status: Planned

Prerequisites:

- [TASK-03 — Configuración y lifecycle](TASK-03-configuration-and-lifecycle.md)
- [TASK-04 — Contrato y middleware HTTP](TASK-04-http-contract-and-middleware.md)

Next:

- [TASK-06 — Películas populares](TASK-06-popular-movies.md)

## Contexto

La aplicación necesita comunicarse con TMDB sin filtrar su contrato hacia handlers. Todavía no existe un caso de uso consumidor, por lo que no corresponde crear una interfaz abstracta en esta tarea.

## Objetivo

Construir y probar un cliente TMDB concreto, reutilizable y acotado por contexto y timeout.

## Alcance

- Incorporar URL base, token, idioma y timeout a la configuración validada.
- Crear un único `http.Client` reutilizable.
- Implementar operaciones concretas para popular, búsqueda y detalle según el contrato oficial vigente.
- Definir DTO externos solo para los campos utilizados.
- Transformar respuestas hacia modelos neutrales o resultados internos mínimos.
- Clasificar: no encontrado, no autorizado, rate limited, timeout, indisponibilidad y respuesta inválida.
- Probar el adaptador con `httptest.Server`.
- Registrar proveedor, operación, estado y `duration_ms` sin secretos.

## Fuera de alcance

- Exponer endpoints públicos de películas.
- Crear `MovieProvider` antes de que exista el servicio consumidor.
- Reintentos automáticos, circuit breaker o caché.
- Replicar todo el SDK o todos los campos de TMDB.
- Consumir TMDB real desde tests.

## Archivos probablemente afectados

- Configuración y `.env.example`.
- Paquete concreto del adaptador, por ejemplo `internal/tmdb`.
- Modelos internos estrictamente necesarios.
- Pruebas con servidor simulado.

## Pasos esperados

1. Verificar la documentación oficial de TMDB y registrar cualquier decisión relevante.
2. Extender configuración sin imprimir el token.
3. Diseñar un constructor que reciba configuración y, si mejora las pruebas, un cliente HTTP.
4. Implementar requests con contexto y autenticación.
5. Decodificar únicamente respuestas esperadas y cerrar cuerpos correctamente.
6. Traducir errores externos al conjunto mínimo interno.
7. Cubrir rutas, query, headers, timeouts y payloads inválidos con `httptest`.

## Restricciones

- Usar `net/http`, `context`, `encoding/json`, `errors` y `httptest`.
- No crear una interfaz para `http.Client` si `httptest.Server` o inyección de transporte resuelve las pruebas.
- No exponer DTO TMDB fuera del adaptador.
- No registrar `Authorization`, token ni cuerpos completos.
- No implementar paginación multipágina.

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

- Todas las requests usan un cliente reutilizable, contexto y timeout.
- El token solo proviene de configuración y nunca aparece en logs o errores.
- Los DTO externos quedan encapsulados.
- Los seis errores mínimos pueden distinguirse mediante `errors.Is`, tipos pequeños u otra solución idiomática simple.
- Los tests no usan Internet y cubren timeout y respuesta inválida.
- No existe todavía una interfaz sin consumidor ni endpoints funcionales públicos.

## Resultado esperado

Un adaptador TMDB concreto y confiable que los casos de uso podrán consumir sin conocer detalles externos.

## Siguiente tarea

Detenerse e informar resultados. Continuar después con [TASK-06](TASK-06-popular-movies.md).
