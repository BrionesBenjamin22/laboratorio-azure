# TASK-07 — Primitivas de autenticación

Status: Planned

Prerequisites:

- [TASK-06](TASK-06-user-persistence.md)

Next:

- [TASK-08](TASK-08-user-registration.md)

## Contexto y objetivo

Seleccionar e implementar primitivas actuales de password hashing y JWT, junto con la persistencia de sesiones, antes de crear flujos HTTP.

## Alcance

- Verificar recomendaciones oficiales y elegir bibliotecas mantenidas.
- Hash y verificación de contraseñas con parámetros documentados.
- Emisión y validación de access tokens con sujeto, rol, `iat`, `exp`, `jti`, issuer y audience.
- Generación segura de refresh tokens opacos y almacenamiento de su hash.
- Política de contraseña con límites mínimo y máximo.
- Migración y repositorio de sesión con usuario, hash de refresh, expiración, revocación y timestamps.
- Índices para validación y revocación sin almacenar tokens reutilizables.

## Fuera de alcance

- Registro, login, rotación, cookies y endpoints públicos.

## Archivos probablemente afectados

- Dependencias, configuración de auth, migración, repositorio de sesiones, servicios criptográficos y tests.

## Pasos esperados

Investigar fuentes oficiales, documentar elección, implementar primitivas pequeñas, crear persistencia de sesión y probar expiración, claims y entradas inválidas.

## Restricciones

- No implementar criptografía propia ni registrar secretos o tokens.

## Tests y validaciones

Ejecutar suite, migraciones y calidad; cubrir hash único, verificación, firma, issuer, audience, expiración, manipulación y constraints de sesión.

## Criterios de aceptación

- Algoritmos y parámetros justificados; tokens inválidos fallan de forma segura y las sesiones solo persisten hashes.

## Resultado esperado

Primitivas probadas. Detenerse antes de TASK-08.
