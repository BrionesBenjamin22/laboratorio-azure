# Bootstrap inicial del repositorio — Azure Kubernetes Lab

## 1. Objetivo de esta tarea

Preparar y estructurar el repositorio raíz del laboratorio antes de comenzar el desarrollo de las aplicaciones.

Este laboratorio tiene como objetivo principal practicar:

- desarrollo y containerización de servicios;
- Docker / OCI images;
- registries de contenedores;
- Kubernetes;
- manifiestos declarativos;
- networking y Services;
- configuración mediante ConfigMaps y Secrets;
- persistencia;
- escalamiento;
- resiliencia;
- observabilidad;
- CI/CD;
- despliegue sobre infraestructura alojada en Azure.

Las aplicaciones son únicamente cargas de trabajo destinadas a experimentar con estas tecnologías.

En esta etapa NO se debe desarrollar ninguna aplicación.

---

# 2. Aplicaciones previstas

El repositorio contendrá posteriormente tres servicios.

## movies-api

Tecnología:

- Go

Responsabilidad:

- consumir la API de TMDB;
- exponer información de películas;
- permitir filtros, búsqueda y consultas simples.

Objetivo técnico posterior:

- aprender Go;
- aplicación stateless;
- containerización;
- replicas;
- load balancing;
- Horizontal Pod Autoscaling.

---

## weather-api

Tecnología:

- Python;
- FastAPI.

Responsabilidad:

- consultar información meteorológica mediante una API externa;
- permitir consultas por ciudad.

Objetivo técnico posterior:

- configuración mediante variables de entorno;
- ConfigMaps;
- Secrets;
- health checks;
- aplicación stateless.

---

## secure-notes-api

Tecnología:

- Python;
- FastAPI;
- PostgreSQL.

Responsabilidad:

- registro de usuarios;
- autenticación;
- JWT;
- gestión de notas privadas por usuario.

Objetivo técnico posterior:

- persistencia;
- PostgreSQL;
- Secrets;
- Persistent Volumes;
- Stateful workloads.

---

# 3. Estado actual del repositorio

La raíz actualmente tiene aproximadamente esta estructura:

azure-lab/
├── .github/
├── .kombai/
├── apps/
├── docs/
├── kubernetes/
├── .gitignore
├── MakeFile
└── README.md

El repositorio Git ya está inicializado.

No reinicializar Git.

---

# 4. Objetivo estructural

Analizar el repositorio existente y preparar una estructura coherente para un monorepo destinado al laboratorio.

La estructura conceptual buscada es:

azure-lab/
├── .github/
│   └── workflows/
│
├── apps/
│   ├── movies-api/
│   ├── weather-api/
│   └── secure-notes-api/
│
├── kubernetes/
│
├── docs/
│
├── scripts/
│
├── .editorconfig
├── .gitattributes
├── .gitignore
├── Makefile
└── README.md

No crear estructuras internas innecesarias dentro de las aplicaciones todavía.

Las carpetas de las aplicaciones pueden permanecer vacías o contener únicamente documentación mínima si fuese necesaria.

---

# 5. Restricciones de esta etapa

NO:

- implementar endpoints;
- generar código Go;
- generar código Python;
- generar modelos;
- implementar FastAPI;
- implementar clientes HTTP;
- configurar TMDB;
- configurar APIs meteorológicas;
- configurar PostgreSQL;
- implementar JWT;
- generar Dockerfiles;
- crear imágenes;
- crear manifests Kubernetes;
- configurar CI/CD;
- desplegar recursos;
- instalar dependencias automáticamente;
- ejecutar comandos que modifiquen software global del sistema.

Esta etapa es exclusivamente de preparación del repositorio.

---

# 6. Archivos raíz a analizar

## .gitignore

Analizar el `.gitignore` existente y completarlo teniendo en cuenta que este monorepo utilizará posteriormente:

- Go;
- Python;
- FastAPI;
- entornos virtuales Python;
- pytest;
- coverage;
- variables `.env`;
- Visual Studio Code;
- Windows;
- Linux;
- Docker;
- Kubernetes;
- archivos temporales;
- binarios;
- logs;
- archivos generados por herramientas;
- credenciales y secretos.

Nunca deben ignorarse archivos que deban formar parte de la definición reproducible del proyecto.

Especial atención a NO versionar:

- `.env`;
- claves privadas;
- kubeconfigs reales;
- Secrets Kubernetes reales;
- credenciales;
- tokens;
- binarios compilados;
- entornos virtuales.

Sí podrán existir archivos de ejemplo como:

- `.env.example`;
- `secret.example.yaml`.

No introducir reglas extremadamente amplias que puedan ocultar accidentalmente archivos importantes.

---

## .editorconfig

Determinar si conviene crear `.editorconfig`.

El objetivo es asegurar comportamiento consistente entre Windows y Linux respecto de:

- encoding;
- final de línea;
- indentación;
- newline final;
- whitespace.

El proyecto será trabajado desde Windows pero posteriormente ejecutado mayormente sobre Linux.

---

## .gitattributes

Analizar si conviene incorporar `.gitattributes` para evitar problemas CRLF/LF entre Windows y Linux.

El repositorio será editado principalmente desde Windows y los contenedores / Kubernetes ejecutarán Linux.

Priorizar una política consistente y explicarla.

---

## Makefile

Analizar el `MakeFile` existente.

Verificar también que el nombre sea convencional:

Makefile

y no:

MakeFile

No agregar todavía tareas asociadas a aplicaciones que no existen.

Si se conserva, puede prepararse únicamente una estructura básica o dejarse documentado para etapas posteriores.

Debe tenerse en cuenta que:

- Bash/Linux puede utilizar `make` directamente;
- PowerShell normalmente no incluye `make`.

No asumir que `make` está disponible en Windows.

---

## README.md

Analizar el README existente.

Mejorarlo únicamente a nivel de estructura del laboratorio.

Debe describir como mínimo:

- propósito del laboratorio;
- arquitectura conceptual;
- aplicaciones previstas;
- stack tecnológico;
- fases generales;
- estructura del repositorio;
- estado actual.

No documentar funcionalidades que todavía no existen como si estuvieran implementadas.

---

# 7. Dependencias y herramientas

Analizar qué herramientas serán necesarias durante el laboratorio.

Clasificarlas en:

## Necesarias ahora

Herramientas necesarias para comenzar el desarrollo.

Ejemplos a evaluar:

- Git;
- Go;
- Python;
- administrador de paquetes Python;
- Visual Studio Code.

## Necesarias posteriormente

Ejemplos:

- Docker o Podman;
- kubectl;
- MicroK8s;
- herramientas de Azure;
- registry CLI;
- herramientas de observabilidad;
- herramientas de carga.

No instalar ninguna herramienta automáticamente.

Primero verificar qué herramientas ya están disponibles cuando sea posible.

---

# 8. Go

La primera aplicación que se desarrollará será:

apps/movies-api

No implementar código.

Analizar únicamente cómo deberá inicializarse como módulo Go.

Indicar posteriormente el comando apropiado:

go mod init ...

pero NO ejecutarlo sin autorización.

Explicar qué nombre de módulo debería utilizarse en función de la URL real del repositorio Git.

No inventar una URL de GitHub.

Si la URL del remote puede obtenerse mediante Git, utilizar:

git remote -v

para determinar el nombre correcto del módulo.

---

# 9. Python

Las aplicaciones:

apps/weather-api
apps/secure-notes-api

utilizarán Python + FastAPI.

En esta etapa no instalar FastAPI ni crear código.

Analizar qué estrategia de dependencias se utilizará posteriormente.

Considerar opciones como:

- `venv` + pip;
- `pyproject.toml`;
- uv.

Elegir una estrategia simple, moderna y adecuada para un laboratorio.

No mezclar varios administradores de dependencias sin necesidad.

Las dependencias deben estar aisladas por aplicación y NO instalarse globalmente.

---

# 10. Kubernetes

La carpeta:

kubernetes/

debe permanecer separada de:

apps/

La intención es que:

- `apps/` contenga código de aplicación;
- `kubernetes/` contenga posteriormente definición declarativa de infraestructura Kubernetes.

No generar manifests todavía.

Puede incorporarse únicamente documentación mínima si aporta claridad.

---

# 11. Scripts

Evaluar la creación de:

scripts/

Será utilizada posteriormente para automatizaciones como:

- build;
- push;
- deploy;
- cleanup;
- health checks.

No crear scripts funcionales todavía salvo que sean estrictamente necesarios para preparar el repositorio.

---

# 12. CI/CD

Mantener:

.github/workflows/

como ubicación destinada a GitHub Actions.

No crear pipelines todavía.

CI/CD será incorporado después de:

1. desarrollar las aplicaciones;
2. probarlas localmente;
3. containerizarlas;
4. desplegarlas manualmente en Kubernetes.

---

# 13. Metodología de trabajo

Antes de modificar archivos:

1. inspeccionar la estructura actual;
2. inspeccionar el contenido del README;
3. inspeccionar `.gitignore`;
4. inspeccionar `MakeFile` / `Makefile`;
5. inspeccionar Git;
6. verificar el remote configurado;
7. identificar archivos existentes que no deberían eliminarse.

Después realizar únicamente cambios correspondientes a la organización inicial del repositorio.

No eliminar contenido existente sin justificarlo.

---

# 14. Salida requerida

Al finalizar la preparación mostrar un resumen con esta estructura.

## Cambios realizados

Explicar:

- archivos creados;
- archivos modificados;
- carpetas creadas;
- archivos renombrados;
- decisiones tomadas.

## Estructura resultante

Mostrar el árbol final del repositorio.

## Herramientas detectadas

Indicar qué herramientas pudieron verificarse y sus versiones.

Por ejemplo:

Git:
Go:
Python:
Docker:
kubectl:

No es obligatorio que todas estén instaladas todavía.

## Herramientas pendientes

Indicar qué herramientas faltan y en qué etapa serán necesarias.

## Comandos que debe ejecutar el usuario

NO ejecutar automáticamente estos comandos.

Para cada operación necesaria, proporcionar:

### PowerShell

```powershell
<comandos>
```

### Bash

```bash
<comandos>
```
