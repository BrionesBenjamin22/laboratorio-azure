# TASK-10 — Suite final y documentación

Status: Planned

Prerequisites:

- [TASK-09 — Hardening](TASK-09-hardening.md)

Next:

- Ninguna tarea de aplicación. El siguiente bloque será una planificación separada de containerización.

## Contexto

Todas las capacidades V1 deben estar implementadas antes de esta tarea. El cierre requiere verificar el módulo completo y convertir su README operativo en la fuente de verdad para consumidores y mantenedores.

## Objetivo

Ejecutar la validación final, completar casos faltantes y documentar ejecución, contrato, límites y decisiones del módulo.

## Alcance

- Revisar la cobertura de servicios, handlers, validaciones y adaptador.
- Agregar únicamente pruebas faltantes necesarias para los criterios V1.
- Ejecutar formato, tests, vet y race cuando el entorno lo permita.
- Realizar pruebas manuales de los cinco endpoints.
- Actualizar `apps/movies-api/README.md`.
- Documentar configuración, comandos, contrato, ejemplos, errores y limitaciones.
- Registrar explícitamente la semántica de paginación truncada.
- Confirmar que el repositorio no contiene secretos ni artefactos generados.

## Fuera de alcance

- Refactors estéticos sin beneficio comprobable.
- Nuevos endpoints o dependencias.
- Frontend, CORS o decisiones UI.
- Dockerfile, manifiestos, CI/CD o despliegue.
- Pruebas contra TMDB real como requisito de la suite.

## Archivos probablemente afectados

- Pruebas del módulo.
- `apps/movies-api/README.md`.
- `.env.example` si la documentación y configuración difieren.
- Código existente solo para corregir defectos detectados por la validación.

## Pasos esperados

1. Comparar implementación con el README de planificación y las tareas previas.
2. Ejecutar toda la suite sin red externa.
3. Revisar casos de error, timeout y request inválida.
4. Ejecutar una prueba manual local con un entorno controlado.
5. Completar el README operativo sin copiar toda la planificación.
6. Verificar formato, estado Git y ausencia de secretos.
7. Informar resultados, comandos y limitaciones.

## Restricciones

- No cambiar el contrato V1 sin documentar el hallazgo que lo exige.
- No depender de una credencial real para tests automatizados.
- No presentar comandos no ejecutados como validados.
- No comenzar containerización en la misma ejecución.

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

Si no hubo archivos Go modificados en esta tarea, verificar formato sin una reescritura innecesaria mediante la estrategia disponible en el entorno.

## Criterios de aceptación

- Popular, búsqueda, detalle, live y ready pasan pruebas automáticas y manuales.
- La suite no consume TMDB real.
- `go test ./...` y `go vet ./...` pasan.
- Race pasa o su imposibilidad queda documentada con causa concreta.
- El README operativo permite configurar y ejecutar el módulo desde un entorno limpio.
- La paginación, el idioma fijo, readiness y graceful shutdown están documentados sin ambigüedad.
- No existen decisiones frontend dentro del contrato backend.
- No se agregaron artefactos de fases posteriores.

## Resultado esperado

Movies API V1 validada y documentada, lista para que una tarea futura diseñe su containerización.

## Commit sugerido

No ejecutar automáticamente sin autorización:

```text
feat(movies-api): implementar consultas de películas con TMDB
```

## Siguiente tarea

Detenerse e informar el cierre del módulo. No iniciar Docker ni Kubernetes hasta que exista y se apruebe su planificación específica.
