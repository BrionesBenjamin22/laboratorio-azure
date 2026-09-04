# TASK-15 — Listar notas

Status: Planned

Prerequisites:

- [TASK-14](TASK-14-create-note.md)

Next:

- [TASK-16](TASK-16-note-detail.md)

## Contexto y objetivo

Implementar `GET /api/v1/notes?page=1` con aislamiento y paginación en PostgreSQL.

## Alcance

- Listar únicamente notas activas del propietario autenticado.
- Validar `page`; devolver como máximo 9 resultados.
- Ordenar de forma determinista por campos permitidos internamente.
- Calcular metadata sin cargar todas las notas.
- Evitar N+1 y contenido innecesario si el listado usa resumen.

## Fuera de alcance

- `page_size`, búsqueda, filtros avanzados y notas eliminadas.

## Archivos probablemente afectados

- Repositorio, servicio, ruta, schemas y tests.

## Pasos esperados

Definir query paginada, contrato de listado, orden estable y pruebas de aislamiento.

## Restricciones

- Paginar en base; no interpolar columnas de ordenamiento desde input libre.

## Tests y validaciones

Cubrir páginas vacías, múltiples usuarios, más de 9 notas, soft delete y page inválida.

## Criterios de aceptación

- Nunca se devuelven notas ajenas o eliminadas y cada página contiene hasta 9.

## Resultado esperado

Listado seguro y eficiente. Detenerse antes de TASK-16.
