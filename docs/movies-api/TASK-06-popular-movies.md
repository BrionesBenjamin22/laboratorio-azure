# TASK-06 — Películas populares

Status: Planned

Prerequisites:

- [TASK-05 — Adaptador TMDB](TASK-05-tmdb-adapter.md)

Next:

- [TASK-07 — Búsqueda de películas](TASK-07-search-movies.md)

## Contexto

El cliente TMDB existe, pero todavía no hay un flujo funcional expuesto. Esta tarea crea el primer caso de uso completo y, por primera vez, una abstracción tiene un consumidor concreto.

## Objetivo

Implementar `GET /api/v1/movies/popular?page=1` con servicio, handler, transformación, paginación explícita y pruebas.

## Alcance

- Definir el modelo público mínimo de película.
- Crear el caso de uso de películas populares.
- Definir cerca del servicio consumidor una interfaz `MovieProvider` mínima que contenga solo la operación necesaria.
- Hacer que el cliente TMDB satisfaga esa interfaz estructuralmente.
- Validar `page` como entero mayor o igual que 1, con valor predeterminado 1.
- Solicitar exactamente una página al proveedor.
- Devolver como máximo los primeros 9 resultados.
- Exponer metadata `page`, `returned` y `has_next`.
- Mapear errores mediante el contrato HTTP existente.

## Fuera de alcance

- `page_size`.
- Combinar páginas de TMDB o mantener continuidad local en bloques de nueve.
- Búsqueda y detalle.
- Caché, favoritos o persistencia.
- Ampliar la interfaz con operaciones todavía no consumidas.

## Archivos probablemente afectados

- Paquete del dominio o caso de uso de películas, por ejemplo `internal/movies`.
- Router y handler HTTP.
- Adaptador TMDB solo si requiere satisfacer el contrato consumidor.
- Pruebas unitarias y HTTP.

## Pasos esperados

1. Revisar el resultado neutral producido por el adaptador.
2. Definir el contrato mínimo consumido por el servicio.
3. Implementar el truncado determinista a 9 resultados.
4. Calcular `has_next` con información confiable del proveedor.
5. Registrar la ruta y responder con el contrato V1.
6. Probar el servicio con un provider falso pequeño, sin framework de mocks.
7. Probar handler, validación, truncado y errores.

## Restricciones

- La interfaz reside junto al consumidor, no dentro de TMDB.
- La interfaz debe contener únicamente el método popular en este incremento.
- No filtrar DTO ni errores crudos de TMDB.
- No presentar `page=2` como el segundo bloque continuo de nueve resultados.
- No aceptar idioma por request.

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

Probar manualmente páginas válida, predeterminada, cero, negativa y no numérica.

## Criterios de aceptación

- El endpoint devuelve como máximo 9 películas.
- `page` corresponde directamente a una página de TMDB.
- La respuesta informa `page`, `returned` y `has_next` sin prometer continuidad local.
- Entradas inválidas no invocan al provider.
- El servicio puede probarse con un fake escrito con biblioteca estándar.
- El adaptador satisface una interfaz definida por el consumidor.
- No se agregó `page_size`, idioma dinámico ni paginación multipágina.

## Resultado esperado

El primer endpoint funcional completo, pequeño y representativo de la arquitectura evolutiva.

## Siguiente tarea

Detenerse e informar resultados. Continuar después con [TASK-07](TASK-07-search-movies.md).
