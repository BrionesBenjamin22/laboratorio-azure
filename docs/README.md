# Plan de desarrollo de módulos

## Objetivo

Esta carpeta contiene las especificaciones ejecutables del laboratorio. Cada documento define el alcance de un bloque de trabajo, sus restricciones, entregables y criterios de aceptación.

Las tareas deben ejecutarse de forma incremental. No se debe comenzar un módulo nuevo hasta validar y cerrar el anterior.

## Orden de ejecución

| Orden | Documento | Estado | Resultado esperado |
| --- | --- | --- | --- |
| 0 | [`00-repository-bootstrap.md`](00-repository-bootstrap.md) | Completado | Repositorio base normalizado. |
| 1 | [`01-movies-api.md`](01-movies-api.md) | Pendiente | API de consulta de películas desarrollada en Go. |
| 2 | [`02-weather-api.md`](02-weather-api.md) | Pendiente | API meteorológica desarrollada en Python y FastAPI. |
| 3 | [`03-secure-notes-api.md`](03-secure-notes-api.md) | Pendiente | API de notas privadas con autenticación y PostgreSQL. |

La contenerización coordinada, los manifiestos de Kubernetes, las interfaces web y CI/CD se documentarán como tareas posteriores. Cada servicio debe quedar preparado para esas etapas, pero no debe adelantar recursos globales que todavía no estén definidos.

## Convenciones compartidas

### Contrato HTTP

- Versionar los endpoints funcionales bajo `/api/v1`.
- Reservar `/health/live` y `/health/ready` para comprobaciones de salud.
- Utilizar JSON y nombres de campos en `snake_case`.
- Expresar fechas y horas en UTC con formato ISO 8601.
- Incluir un identificador de solicitud en logs y respuestas de error.
- No exponer mensajes internos de proveedores, trazas, consultas SQL ni secretos.

Respuesta satisfactoria individual:

```json
{
  "data": {},
  "message": "Operación realizada correctamente."
}
```

Respuesta satisfactoria paginada:

```json
{
  "data": [],
  "meta": {
    "page": 1,
    "page_size": 9,
    "total": 0,
    "total_pages": 0
  }
}
```

Respuesta de error:

```json
{
  "error": {
    "code": "RESOURCE_UNAVAILABLE",
    "message": "Lo sentimos. No pudimos recuperar la información. Intente nuevamente.",
    "request_id": "identificador-de-la-solicitud",
    "details": []
  }
}
```

`details` será opcional y solo podrá contener errores seguros de validación. Los listados destinados a pantallas home admitirán como máximo 9 elementos por página. Los historiales de cambios utilizarán 3 elementos por página.

### Arquitectura

Cada servicio debe separar, como mínimo:

- configuración;
- transporte HTTP;
- validaciones;
- casos de uso o servicios de aplicación;
- modelos de dominio y contratos de transferencia;
- integraciones externas o persistencia;
- manejo uniforme de errores;
- observabilidad;
- pruebas.

Las dependencias externas deben quedar detrás de interfaces propias. Ningún handler o controller debe consumir directamente un proveedor externo o ejecutar consultas de persistencia.

### Configuración y seguridad

- Configurar los servicios mediante variables de entorno.
- Proveer únicamente archivos `.env.example` sin valores sensibles.
- Validar la configuración obligatoria al iniciar y fallar con un mensaje seguro.
- Validar y normalizar toda entrada en el límite HTTP.
- Definir límites de longitud, rangos y valores admitidos.
- No registrar tokens, contraseñas, claves, contenido privado ni respuestas completas de proveedores.
- Aplicar timeouts a dependencias externas y operaciones de persistencia.
- Evitar dependencias globales; cada aplicación administra las propias.

### Observabilidad y operación

- Emitir logs estructurados con nivel, fecha, servicio, operación y `request_id`.
- Diferenciar disponibilidad del proceso y disponibilidad de sus dependencias.
- Preparar cierre ordenado del servidor.
- Mantener compatibilidad con Docker, variables de entorno, proxy reverso y despliegue futuro en Kubernetes.
- No crear manifiestos Kubernetes ni pipelines globales dentro de las tareas de aplicación.

### Calidad y documentación

Cada módulo debe incluir:

- pruebas unitarias de reglas y servicios;
- pruebas de integración de adaptadores relevantes;
- pruebas HTTP de los flujos principales y de error;
- instrucciones de ejecución local;
- contrato de endpoints y ejemplos seguros;
- variables de entorno documentadas;
- decisiones de seguridad y limitaciones conocidas.

Al finalizar cada módulo se realizará una prueba manual y la suite backend correspondiente. El commit no debe ejecutarse automáticamente salvo autorización explícita del usuario.

## Funcionalidades fuera del alcance actual

- Frontends.
- Manifiestos Kubernetes.
- Despliegues en Azure.
- Pipelines CI/CD.
- Observabilidad centralizada.
- Pruebas de carga.

Estos bloques se definirán después de disponer de los servicios probados localmente.
