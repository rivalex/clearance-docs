# Arquitectura

## Cómo funciona la resolución

`resolveFor()` es el núcleo de la autorización contextual de Clearance - consulta [Autorización Contextual](/es/features/contextual-authorization) para la API completa. Combina tres fuentes, en este orden de precedencia:

1. **Concesiones de rol contextual** - permisos de los roles que el usuario posee específicamente dentro de este contexto (`clr_role_ctx`).
2. **Concesiones de rol global** - permisos de los roles Spatie asignados globalmente al usuario (de modo que `super_admin` y otros roles globales satisfacen `canIn()` para cualquier contexto).
3. **Overrides por contexto** (`clr_ctx_overrides`) - `forced_on` añade un permiso encima de lo anterior; `forced_off` lo elimina, incluso si el permiso fue concedido por un rol global. Una denegación siempre gana.

::: warning Una denegación siempre gana
`forced_off` anula cualquier permiso concedido por un rol global o una concesión de rol contextual. No hay forma de "superar en rango" a un override `forced_off` para el mismo usuario/permiso/contexto, salvo eliminándolo.
:::

## Base de datos

Clearance posee 6 tablas y nunca altera las tablas núcleo de Spatie (`roles`, `permissions`, `model_has_roles`, `model_has_permissions`, `role_has_permissions`). Todas las FK que referencian `roles.id`/`permissions.id` hacen cascade on delete salvo que se indique lo contrario; las tablas de Spatie no tienen FKs que apunten de vuelta a `clr_*`.

| Tabla | Columnas clave |
|---|---|
| `clr_meta` | `subject_type`, `subject_key` (únicos en conjunto) - `display_name`, `description`, `icon_svg`, `color` - metadatos de visualización para un rol o guard |
| `clr_role_meta` | `role_id` (único, FK -> `roles`) - `is_locked`, `scope` (`global`\|`contextual`), `context_types` (json) - `parent_role_id` (FK anulable -> `roles`, `ON DELETE SET NULL`) - rol techo |
| `clr_guards` | `name` (único), `driver`, `provider` - guards gestionados vía el panel |
| `clr_settings` | `key` (único), `value` - almacén clave/valor en tiempo de ejecución (rol predeterminado, etc.) |
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - asignaciones de rol contextual, únicas por `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) - overrides de permiso por usuario y por contexto |

## Nunca toca las tablas de Spatie

Clearance está diseñado para convivir junto a `spatie/laravel-permission`, no para reemplazarlo. Cada registro `Role` y `Permission` que un panel de Clearance crea o edita es un modelo Spatie genuino, almacenado en las propias tablas de Spatie. Clearance nunca ejecuta una migración que añada una columna a, o escriba una FK desde, ninguna de `roles`, `permissions`, `model_has_roles`, `model_has_permissions`, o `role_has_permissions`.

Todos los datos propios de Clearance - metadatos de visualización, alcance y techos de rol, asignaciones contextuales, overrides por contexto, definiciones de guard, y configuración - viven exclusivamente en las 6 tablas `clr_*` anteriores. Esto significa que:

- Desinstalar Clearance nunca arriesga corromper tus datos existentes de rol/permiso de Spatie.
- Cualquier código que ya lea los modelos o tablas de Spatie directamente sigue funcionando sin modificaciones.
- Las funciones contextuales y de techo de Clearance son capas puramente aditivas resueltas en tiempo de consulta (vía `resolveFor()` y `RoleService`), no incorporadas al esquema de Spatie.

## Relacionado

- [Autorización Contextual](/es/features/contextual-authorization) - `canIn()`, `hasRoleIn()`, directivas Blade, `ContextService`
- [Roles](/es/features/roles) - alcance y roles techo, `RoleService::setParent()`
- [Migración](/es/guide/migration) - rellenar `clr_meta`/`clr_role_meta`/`clr_guards` sobre una instalación de Spatie existente
- [Seguridad](/es/guide/security) - cómo el modelo de techo y override previene la escalada de privilegios
