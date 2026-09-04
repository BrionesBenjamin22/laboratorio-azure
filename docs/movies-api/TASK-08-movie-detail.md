# TASK-08 — Detalle de película

Status: Planned

Prerequisites:

- [TASK-07 — Búsqueda de películas](TASK-07-search-movies.md)

Next:

- [TASK-09 — Hardening](TASK-09-hardening.md)

## Contexto

Popular y búsqueda cubren listados. Falta completar el contrato V1 con el detalle individual y distinguir correctamente recursos inexistentes.

## Objetivo

Implementar `GET /api/v1/movies/{movie_id}` con validación de identificador, modelo de detalle y mapeo seguro de `404`.

## Alcance

- Registrar la ruta parametrizada usando capacidades de `net/http` disponibles en la versión del módulo.
- Validar `movie_id` como entero positivo.
- Incorporar la operación de detalle a la interfaz consumidora.
- Definir un modelo público sin exponer DTO TMDB.
- Mapear recurso inexistente a `404 Not Found`.
- Mapear los demás errores con el contrato existente.
- Probar handler, servicio y conexión con el adaptador.

## Fuera de alcance

- Créditos, recomendaciones, trailers o endpoints relacionados.
- Persistencia o caché del detalle.
- Slugs y búsqueda por título en la ruta.
- Campos de TMDB que el contrato V1 no consuma.

## Archivos probablemente afectados

- Servicio y modelo de películas.
- Interfaz consumidora.
- Router y handler.
- Adaptador TMDB si requiere completar transformación.
- Pruebas correspondientes.

## Pasos esperados

1. Confirmar el soporte de patrones y parámetros de ruta de la versión Go declarada.
2. Implementar parsing y validación del identificador.
3. Extender la interfaz con la operación consumida.
4. Transformar únicamente campos públicos acordados.
5. Traducir no encontrado sin incluir cuerpo ni mensaje de TMDB.
6. Probar identificadores válidos, cero, negativos, no numéricos e inexistentes.

## Restricciones

- Mantener `net/http`; no introducir un router externo para una sola ruta dinámica.
- No devolver estructuras externas directamente.
- No revelar diferencias entre errores internos sensibles.
- No crear abstracciones genéricas de entidades que solo sirven a películas.

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

- Identificadores inválidos responden `400 Bad Request` sin llamar al provider.
- Una película inexistente responde `404 Not Found` con error público uniforme.
- El detalle utiliza modelos propios y campos opcionales consistentes.
- El endpoint conserva `request_id` y logging estructurado.
- No se agregaron endpoints o dependencias fuera del alcance.

## Resultado esperado

El contrato funcional V1 completo: popular, búsqueda y detalle.

## Siguiente tarea

Detenerse e informar resultados. Continuar después con [TASK-09](TASK-09-hardening.md).
