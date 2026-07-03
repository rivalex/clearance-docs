# Seguridad

Clearance pasó por una auditoría de seguridad dedicada previa a su publicación, además de dos rondas adicionales de refuerzo antes del lanzamiento. Aspectos destacados: techos de escalada de privilegios en cada ruta que otorga permisos (tanto en concesiones directas como en la sincronización a nivel de rol), overrides de contexto con criterio de denegación autoritaria, un sanitizador de SVG basado en lista de permitidos, un namespace de permisos `clearance-*` protegido, y directivas Blade seguras para invitados.

## Acceso al panel y permisos de escritura granulares

El acceso de lectura a todo el panel requiere el permiso `clearance-access` (verificado vía `can()`, nunca `hasRole()`). Las operaciones de escritura en cada sección están controladas por un permiso dedicado y delegable - todos creados por `clearance:install` y asignados a `super_admin`:

| Permiso | Controla |
|---|---|
| `clearance-permissions-write` | Crear / editar / eliminar permisos |
| `clearance-roles-write` | Crear / editar / eliminar roles y sus asignaciones de permisos |
| `clearance-guards-write` | Crear / editar / eliminar guards |
| `clearance-settings-write` | Editar metadatos de visualización, iconos, colores, rol predeterminado |
| `clearance-users-write` | Asignar / eliminar roles para un usuario específico |

Si una fila de permiso granular no existe en absoluto en la base de datos (un estado heredado previo a la siembra), la comprobación recurre a `clearance-access` por compatibilidad hacia atrás.

## Namespace `clearance-*` protegido

Los permisos `clearance-*` están reservados: no pueden crearse, renombrarse, eliminarse, ni añadirse a ningún rol excepto a través del propio flujo de instalación del paquete - ni siquiera por un actor `super_admin` para el propio rol `super_admin`.

## Techos de escalada de privilegios

Cada ruta que otorga permisos en Clearance aplica un techo para que un actor nunca pueda otorgar más de lo que él mismo posee:

- **Roles techo** - un rol puede declarar un rol padre cuyo conjunto de permisos actúa como límite superior. Cualquier permiso excedente se recorta silenciosamente en `RoleService::setParent()` y en cada sincronización de permisos posterior - sin excepción, sin mensaje de error. Eliminar permisos de un padre propaga la eliminación a cada hijo automáticamente. Consulta [Roles](/es/features/roles) para la API completa de techo.
- **Módulo de Usuarios** - tanto la asignación de roles como las concesiones de permisos directas/de override aplican un techo de privilegio: un actor que no sea `super_admin` no puede modificar sus propias asignaciones, ni puede otorgar un permiso que él mismo no posea. Consulta [Usuarios](/es/features/users).

## Overrides de contexto con criterio de denegación autoritaria

Los overrides de permiso por contexto (`clr_ctx_overrides`) admiten `forced_on` y `forced_off`. `forced_off` siempre gana - incluso sobre un permiso concedido por un rol global. No existe ninguna ruta que permita que una concesión de menor precedencia anule una denegación explícita. Consulta [Arquitectura](/es/guide/architecture#how-resolution-works) para el orden completo de resolución.

## Sanitización de SVG

Cada icono proporcionado por el usuario (metadatos de visualización de rol/guard en el panel de Configuración) pasa por un sanitizador basado en lista de permitidos tanto antes de almacenarse como al renderizarse - no una comprobación única en la carga.

## Directivas Blade seguras para invitados

`@canin` y `@hasrolein` se resuelven de forma segura (devuelven `false`) para un usuario invitado - nunca lanzan una excepción.

## El bypass de Gate es de alcance global

`super_admin_gate_bypass` (predeterminado `false`) controla si el rol `super_admin` evita todas las comprobaciones `can()` / `Gate::allows()` / de política de Laravel globalmente, mediante un hook `Gate::before()`.

::: warning No limitado al panel
Cuando está activado, cualquier usuario que posea `super_admin` evita todas las comprobaciones de Gate en toda la aplicación - no solo las comprobaciones del panel de Clearance. Actívalo de forma deliberada. Consulta [Super Admin](/es/features/super-admin) para la detección de alias y el comportamiento de promoción.
:::

## Detalles de seguridad a tener en cuenta

- `forced_off` siempre gana - incluso sobre un permiso concedido por un rol global.
- Un rol techo solo recorta, nunca concede - establecer un padre permisivo no otorga nada al hijo por sí mismo.
- El bypass de Gate (`super_admin_gate_bypass`) es de alcance global, no limitado al panel.
- El módulo de Usuarios está desactivado por defecto (`modules.users = false`).
- Los permisos `clearance-*` son intocables fuera del flujo de instalación, incluso para `super_admin`.
- Ejecuta siempre `clearance:install`, no un `migrate` simple, en una instalación nueva - consulta [Instalación](/es/guide/installation).

## Relacionado

- [Arquitectura](/es/guide/architecture) - precedencia de resolución y el esquema `clr_*`
- [Roles](/es/features/roles) - roles techo en profundidad
- [Usuarios](/es/features/users) - el techo de privilegio en la asignación de roles/permisos
- [Super Admin](/es/features/super-admin) - bypass de Gate y detección de alias
