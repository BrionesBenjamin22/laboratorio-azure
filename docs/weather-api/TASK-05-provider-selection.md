# TASK-05 — Selección y contrato del proveedor

Status: Planned

Prerequisites:

- [TASK-04 — Contrato y middleware HTTP](TASK-04-http-contract-and-middleware.md)

Next:

- [TASK-06 — Adaptador meteorológico](TASK-06-weather-provider-adapter.md)

## Contexto

No se debe implementar un cliente hasta confirmar qué proveedor satisface el alcance V1 y cómo se desacoplará su contrato.

## Objetivo

Seleccionar el proveedor meteorológico, documentar la decisión y definir el modelo interno mínimo sin realizar todavía requests externas.

## Alcance

- Comparar opciones mediante documentación oficial vigente.
- Evaluar autenticación, cuota gratuita, ubicación por ciudad, localización, unidades y estabilidad.
- Elegir un único proveedor para V1.
- Documentar endpoint requerido, credencial, límites y términos relevantes.
- Definir el contrato interno consumido por el futuro servicio.
- Definir modelo neutral y campos opcionales.
- Actualizar variables previstas.

## Fuera de alcance

- Implementar el cliente HTTP.
- Probar con credenciales reales.
- Diseñar fallback entre proveedores.
- Exponer endpoints funcionales.

## Archivos probablemente afectados

- Documento de decisión dentro del módulo si la comparación lo justifica.
- Modelos o protocolos mínimos, solo si no anticipan implementación.
- README y `.env.example`.

## Pasos esperados

1. Verificar fuentes oficiales.
2. Comparar únicamente criterios relevantes al laboratorio.
3. Elegir y justificar.
4. Mapear el contrato externo al modelo V1.
5. Identificar errores externos que deberán traducirse.

## Restricciones

- No inventar capacidades del proveedor.
- No incluir claves.
- No convertir esta tarea en integración.
- No generalizar para múltiples proveedores sin necesidad actual.

## Validaciones

- Todos los enlaces y afirmaciones técnicas provienen de fuentes oficiales.
- El modelo V1 no replica el payload completo externo.
- La decisión cubre cuota, idioma, unidades y desambiguación de ciudad.
- Tests y linters existentes continúan pasando si se agregan tipos.

## Criterios de aceptación

- Existe un proveedor único y justificado.
- Se conoce el mecanismo seguro de autenticación.
- El contrato interno puede representar éxito y ausencias reales.
- Los límites externos relevantes están documentados.
- No se realizaron llamadas reales ni implementación del adaptador.

## Resultado esperado

Decisión técnica suficiente para implementar un adaptador acotado.

## Siguiente tarea

Detenerse e informar resultados. Continuar con [TASK-06](TASK-06-weather-provider-adapter.md).
