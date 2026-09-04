# TASK-12 — Administración de usuarios

Status: Planned

Prerequisites:

- [TASK-11](TASK-11-current-user-profile.md)

Next:

- [TASK-13](TASK-13-note-persistence.md)

## Contexto y objetivo

Dar significado concreto al rol `admin` sin conceder acceso a notas privadas.

## Alcance

- Definir aprovisionamiento offline de administradores sin endpoint público de elevación.
- Implementar `GET /api/v1/admin/users?page=1`, máximo 9.
- Implementar cambio de estado activo con versión esperada.
- Implementar `GET /api/v1/admin/users/{user_id}/history?page=1`, con 3 eventos por página.
- Impedir desactivar al último administrador activo si la política lo requiere y se justifica.
- Revocar o bloquear renovaciones del usuario desactivado.
- Registrar historial de estado y auditar acciones administrativas en la misma transacción.

## Fuera de alcance

- Leer notas ajenas, cambiar roles por API y eliminar cuentas.

## Archivos probablemente afectados

- Autorización por rol, comando offline, rutas admin, servicios, repositorios y tests.

## Pasos esperados

Definir provisión segura, proteger rutas, paginar en base y actualizar estado transaccionalmente.

## Restricciones

- Ningún payload público crea admin; respuestas no incluyen hashes ni sesiones.

## Tests y validaciones

Cubrir usuario común, admin, paginación 9/3, estado, historial, concurrencia y prohibición de acceso a notas.

## Criterios de aceptación

- Solo admin gestiona estado y consulta historial administrativo; el rol nunca habilita lectura de contenido privado.

## Resultado esperado

Permisos por rol reales y acotados. Detenerse antes de TASK-13.
