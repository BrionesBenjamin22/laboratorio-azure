# TASK-10 — Validación final y documentación

Status: Planned

Prerequisites:

- [TASK-09 — Hardening](TASK-09-hardening.md)

Next:

- Ninguna tarea de aplicación; seguirá una planificación separada de containerización.

## Contexto

El módulo debe cerrarse con una suite reproducible y documentación operativa suficiente.

## Objetivo

Validar Weather API completa y actualizar su README de aplicación sin iniciar infraestructura.

## Alcance

- Completar tests faltantes del contrato V1.
- Ejecutar suite, formato, análisis estático y cobertura acordada.
- Probar manualmente health y clima actual.
- Documentar instalación, configuración, proveedor, contrato, caché, errores y límites.
- Confirmar ausencia de secretos y artefactos generados.

## Fuera de alcance

- Nuevos endpoints o proveedores.
- Frontend, CORS, Docker, Kubernetes, CI/CD y despliegue.
- Requerir Internet en la suite automatizada.

## Archivos probablemente afectados

- Tests faltantes.
- `apps/weather-api/README.md`.
- `.env.example` si existe una discrepancia.
- Código solo para defectos demostrados.

## Pasos esperados

1. Comparar implementación con este índice y tareas.
2. Ejecutar validación completa sin proveedor real.
3. Realizar prueba manual controlada.
4. Completar documentación operativa.
5. Verificar Git y material sensible.
6. Informar resultados y detenerse.

## Restricciones

- No presentar comandos no ejecutados como validados.
- No cambiar contrato sin documentar el defecto.
- No comenzar containerización.

## Validaciones

```powershell
Set-Location apps/weather-api
uv lock --check
uv run pytest
uv run ruff check .
uv run ruff format --check .
```

```bash
cd apps/weather-api
uv lock --check
uv run pytest
uv run ruff check .
uv run ruff format --check .
```

## Criterios de aceptación

- Los tres endpoints pasan pruebas automáticas y manuales.
- La suite no consume el proveedor real.
- Configuración, errores, caché y limitaciones están documentados.
- Dependencias y lockfile son reproducibles.
- No existen secretos ni artefactos de fases posteriores.

## Resultado esperado

Weather API V1 validada y lista para planificar su containerización.

## Commit sugerido

No ejecutar automáticamente sin autorización:

```text
feat(weather-api): implementar consulta meteorológica actual
```

## Siguiente tarea

Detenerse. No iniciar Docker o Kubernetes sin una tarea aprobada.
