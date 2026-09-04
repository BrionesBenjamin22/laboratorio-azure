# TASK-01 — Inicialización del proyecto Python

Status: Planned

Prerequisites:

- [Bootstrap del repositorio](../00-repository-bootstrap.md) completado.

Next:

- [TASK-02 — Aplicación FastAPI base](TASK-02-base-fastapi-application.md)

## Contexto

El módulo solo contiene un README. Necesita una fuente de verdad para versión, dependencias y herramientas antes de desarrollar la aplicación.

## Objetivo

Inicializar un proyecto Python aislado con `uv`, `pyproject.toml`, lockfile y configuración mínima de calidad.

## Alcance

- Verificar Python y disponibilidad de `uv`.
- Instalar `uv` únicamente con autorización si continúa ausente.
- Inicializar el proyecto dentro de `apps/weather-api`.
- Definir paquete fuente y carpeta de tests mínimos.
- Declarar versión de Python.
- Incorporar solo herramientas de formato, análisis y tests aprobadas para las tareas inmediatas.

## Fuera de alcance

- Crear la aplicación FastAPI.
- Agregar cliente HTTP o proveedor.
- Implementar endpoints.
- Crear entorno global, Docker o infraestructura.

## Archivos probablemente afectados

- `apps/weather-api/pyproject.toml`.
- `apps/weather-api/uv.lock`.
- Estructura mínima de paquete y tests si la herramienta la requiere.
- README solo para decisiones verificadas.

## Pasos esperados

1. Inspeccionar herramientas y estado Git.
2. Confirmar estrategia `uv` y versión Python.
3. Inicializar sin plantilla web compleja.
4. Configurar tests, formato y análisis estático mínimos.
5. Crear y sincronizar el entorno local aislado.
6. Verificar que no se instalaron paquetes globalmente.

## Restricciones

- No mezclar administradores de dependencias.
- No agregar dependencias de runtime todavía.
- No crear carpetas sin responsabilidad.
- Solicitar autorización antes de instalar `uv`.

## Validaciones

```powershell
python --version
uv --version
Set-Location apps/weather-api
uv lock --check
uv run python --version
```

```bash
python --version
uv --version
cd apps/weather-api
uv lock --check
uv run python --version
```

## Criterios de aceptación

- El proyecto tiene `pyproject.toml` y lockfile propios.
- El entorno queda aislado en el módulo e ignorado por Git.
- No existen dependencias funcionales prematuras.
- Los comandos de calidad definidos pueden ejecutarse.
- No se modificaron otros módulos.

## Resultado esperado

Base Python reproducible preparada para incorporar FastAPI.

## Siguiente tarea

Detenerse e informar resultados. No comenzar [TASK-02](TASK-02-base-fastapi-application.md).
