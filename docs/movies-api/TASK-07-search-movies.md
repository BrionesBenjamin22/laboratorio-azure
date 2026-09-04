# TASK-07 — Búsqueda de películas

Status: Planned

Prerequisites:

- [TASK-06 — Películas populares](TASK-06-popular-movies.md)

Next:

- [TASK-08 — Detalle de película](TASK-08-movie-detail.md)

## Contexto

El flujo popular establece el patrón completo. La búsqueda debe reutilizarlo y agregar validación fuerte sin introducir un framework general.

## Objetivo

Implementar `GET /api/v1/movies/search?query=matrix&page=1` con validación, paginación truncada y manejo seguro de errores.

## Alcance

- Incorporar la operación de búsqueda al contrato mínimo del consumidor.
- Normalizar espacios exteriores y rechazar búsquedas vacías.
- Definir límites mínimo y máximo de longitud justificados.
- Rechazar caracteres de control.
- Validar `page` con las mismas reglas de popular.
- Solicitar una única página al proveedor y devolver hasta 9 resultados.
- Mantener metadata `page`, `returned` y `has_next`.
- Probar que entradas inválidas no llaman al provider.

## Fuera de alcance

- Sugerencias, autocompletado o historial de búsquedas.
- Sanitización que altere títulos válidos innecesariamente.
- `page_size` o idioma por request.
- Consultar múltiples páginas.
- rate limiting específico para búsqueda.

## Archivos probablemente afectados

- Servicio o caso de uso de películas.
- Interfaz consumidora existente.
- Handler y router.
- Adaptador TMDB si la operación todavía no está conectada.
- Pruebas de validación, servicio y HTTP.

## Pasos esperados

1. Reutilizar parsing de `page` sin crear un paquete genérico prematuro.
2. Definir una función de validación local a la responsabilidad de búsqueda.
3. Extender la interfaz solo con la operación ahora consumida.
4. Implementar el caso de uso y el handler.
5. Mapear errores externos mediante el contrato consolidado.
6. Probar consultas Unicode válidas, espacios, límites y caracteres de control.

## Restricciones

- Utilizar validación escrita con biblioteca estándar.
- No introducir expresiones restrictivas que rechacen nombres internacionales válidos.
- No registrar la consulta completa si la política de logging no la necesita.
- No duplicar el flujo popular; extraer solo aquello que ya tenga dos consumidores reales.

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

- Una consulta válida devuelve hasta 9 resultados.
- Consultas ausentes, vacías, excesivas o con controles reciben `400 Bad Request`.
- Ninguna entrada inválida llama al provider.
- La paginación mantiene la semántica documentada del proveedor truncado.
- La interfaz crece únicamente con la operación de búsqueda necesaria.
- No se incorporaron librerías externas de validación.

## Resultado esperado

Un segundo flujo funcional consistente con popular y con límites de entrada comprobables.

## Siguiente tarea

Detenerse e informar resultados. Continuar después con [TASK-08](TASK-08-movie-detail.md).
