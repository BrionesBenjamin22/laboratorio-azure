# TASK-01 — Inicialización del módulo Go

Status: Planned

Prerequisites:

- [Bootstrap del repositorio](../00-repository-bootstrap.md) completado.

Next:

- [TASK-02 — Servidor HTTP base](TASK-02-base-http-server.md)

## Contexto

`apps/movies-api` solo contiene documentación. Antes de escribir el servidor se necesita un módulo Go identificable y verificable, sin anticipar la arquitectura interna.

## Objetivo

Inicializar el módulo Go con la ruta derivada del remote Git y dejar la mínima base necesaria para las tareas posteriores.

## Alcance

- Inspeccionar el estado del repositorio y la versión activa de Go.
- Confirmar que el remote continúa correspondiendo a `github.com/BrionesBenjamin22/laboratorio-azure`.
- Crear `go.mod` dentro de `apps/movies-api`.
- Confirmar la ruta declarada del módulo.
- Actualizar la documentación del módulo solo si la versión mínima de Go necesita quedar registrada.

## Fuera de alcance

- Crear servidor o archivos `.go` sin una responsabilidad actual.
- Diseñar paquetes internos.
- Agregar dependencias.
- Integrar TMDB.
- Crear `.env`, Dockerfile o infraestructura.

## Archivos probablemente afectados

- `apps/movies-api/go.mod`.
- `apps/movies-api/README.md`, únicamente si requiere registrar una decisión comprobada.

La implementación puede afectar menos archivos si no necesita documentación adicional.

## Pasos esperados

1. Verificar `git status`, remote y versión de Go.
2. Confirmar la ruta exacta del módulo.
3. Inicializar el módulo desde `apps/movies-api`.
4. Inspeccionar el `go.mod` resultante.
5. Verificar que no aparecieron dependencias ni estructuras vacías.

## Restricciones

- No inventar una URL de módulo diferente del remote.
- No ejecutar el comando desde la raíz.
- No agregar librerías o código de aplicación.
- No ejecutar automáticamente un commit sin autorización.

## Comando principal

Ejecutar únicamente después de confirmar el remote y desde la carpeta del servicio:

```powershell
Set-Location apps/movies-api
go mod init github.com/BrionesBenjamin22/laboratorio-azure/apps/movies-api
```

```bash
cd apps/movies-api
go mod init github.com/BrionesBenjamin22/laboratorio-azure/apps/movies-api
```

## Validaciones

```powershell
go version
git remote -v
Set-Location apps/movies-api
go list -m
go env GOMOD
```

```bash
go version
git remote -v
cd apps/movies-api
go list -m
go env GOMOD
```

## Criterios de aceptación

- `go.mod` está dentro de `apps/movies-api`.
- La ruta es `github.com/BrionesBenjamin22/laboratorio-azure/apps/movies-api`, salvo que el remote haya cambiado y se documente la corrección.
- La versión de Go declarada coincide con una versión disponible y justificada.
- No existen dependencias externas ni archivos de implementación.
- El resto del repositorio no fue modificado accidentalmente.

## Resultado esperado

Un módulo Go vacío, válido y listo para recibir el servidor mínimo en TASK-02.

## Siguiente tarea

Detenerse e informar resultados. No comenzar [TASK-02](TASK-02-base-http-server.md) en la misma ejecución.
