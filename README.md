# Laboratorio de Kubernetes en Azure

## Descripción general

Este repositorio contiene un laboratorio práctico orientado al aprendizaje del ciclo de desarrollo, contenerización y despliegue de aplicaciones sobre Kubernetes en infraestructura alojada en Azure.

El proyecto estará compuesto por tres servicios independientes. Las aplicaciones se mantendrán deliberadamente acotadas, ya que funcionan como cargas de trabajo para estudiar la administración del clúster, la definición declarativa de recursos y las prácticas operativas asociadas.

El desarrollo del código contará con asistencia de inteligencia artificial agéntica. La administración inicial de la máquina virtual y de Kubernetes se realizará manualmente con fines educativos.

## Objetivos

- Desarrollar y contenerizar servicios escritos en Go y Python.
- Publicar y administrar imágenes mediante un registro de contenedores.
- Definir recursos de Kubernetes a través de manifiestos declarativos.
- Practicar configuración, gestión de secretos, persistencia, redes y balanceo de carga.
- Aplicar escalamiento, resiliencia, observabilidad y automatización de despliegues.
- Incorporar progresivamente interfaces web y un flujo de integración y entrega continuas.

## Arquitectura conceptual

Los servicios se desarrollarán y desplegarán de forma independiente dentro del clúster:

| Servicio | Tecnología prevista | Responsabilidad principal | Características técnicas previstas |
| --- | --- | --- | --- |
| `movies-api` | Go | Consultar la API de TMDB y exponer información de películas. | Servicio sin estado, réplicas, balanceo de carga y escalamiento horizontal. |
| `weather-api` | Python y FastAPI | Consultar información meteorológica por ciudad mediante una API externa. | Configuración por variables de entorno, ConfigMaps, Secrets y comprobaciones de salud. |
| `secure-notes-api` | Python, FastAPI y PostgreSQL | Gestionar usuarios, autenticación y notas privadas. | JWT, persistencia, Secrets, volúmenes persistentes y cargas con estado. |

La organización separará el código de las aplicaciones de la configuración de infraestructura:

- `apps/` contendrá el código y la configuración propia de cada servicio.
- `kubernetes/` contendrá los manifiestos declarativos del clúster.
- `docs/` reunirá las decisiones técnicas y las tareas del laboratorio.
- `.github/workflows/` se reservará para la automatización de CI/CD.
- `scripts/` podrá incorporarse para tareas operativas reutilizables.

## Tecnologías previstas

- Go.
- Python y FastAPI.
- PostgreSQL.
- Docker o una alternativa compatible con imágenes OCI.
- Kubernetes y `kubectl`.
- Microsoft Azure.
- GitHub Actions.
- Herramientas de observabilidad y pruebas de carga, que se seleccionarán en etapas posteriores.

## Fases generales

1. Preparar y normalizar la estructura inicial del repositorio.
2. Desarrollar y probar localmente los tres servicios.
3. Crear las imágenes de contenedor y publicarlas en un registro.
4. Desplegar manualmente los servicios y sus dependencias en Kubernetes.
5. Incorporar configuración, persistencia, escalamiento, resiliencia y observabilidad.
6. Implementar las interfaces web previstas.
7. Automatizar las validaciones y los despliegues mediante CI/CD.

## Estructura objetivo del repositorio

```text
azure-lab/
├── .github/
│   └── workflows/
├── apps/
│   ├── movies-api/
│   ├── weather-api/
│   └── secure-notes-api/
├── docs/
├── kubernetes/
├── scripts/
├── .editorconfig
├── .gitattributes
├── .gitignore
├── Makefile
└── README.md
```

Esta estructura representa la base del monorepo. Cada carpeta reservada incluye documentación mínima mientras no contenga una implementación funcional.

## Estado actual

El bootstrap inicial del repositorio está completo. Se encuentran definidas la estructura base, la política de archivos ignorados, la configuración de edición y la normalización de finales de línea entre Windows y Linux.

Las aplicaciones, imágenes, manifiestos de Kubernetes y automatizaciones de CI/CD todavía no están implementados. El próximo módulo de desarrollo será `apps/movies-api`.

La especificación de esta etapa está disponible en [`docs/00-repository-bootstrap.md`](docs/00-repository-bootstrap.md).

## Criterios de trabajo

- No documentar funcionalidades previstas como si ya estuvieran implementadas.
- Mantener aisladas las dependencias de cada aplicación.
- Evitar almacenar credenciales, tokens, archivos `.env`, kubeconfigs o Secrets reales en Git.
- Conservar la compatibilidad entre los entornos de desarrollo en Windows y la ejecución sobre Linux.
- Priorizar manifiestos reproducibles, responsabilidades separadas y cambios verificables.
