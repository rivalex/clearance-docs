# Super Admin

El rol `super_admin` se crea automáticamente mediante `clearance:install` y se mantiene sincronizado con todos los permisos `clearance-*`.

```bash
# Install and make user ID 1 a super admin
php artisan clearance:install --user=1
```

Consulta la [guía de Instalación](/es/guide/installation) y la [referencia de comandos Artisan](/es/api/artisan-commands) para la lista completa de flags del instalador.

## Bypass de Gate (opcional)

`super_admin_gate_bypass` controla si el rol `super_admin` evita todas las comprobaciones `can()` / `Gate::allows()` / de política de Laravel globalmente, mediante un hook `Gate::before()`. Predeterminado: `false`. El bypass es opcional. Para activarlo:

```php
// config/clearance.php
'super_admin_gate_bypass' => true,
```

::: danger De alcance global, no limitado al panel
Cuando está activado, cualquier usuario que posea `super_admin` evita **todas** las comprobaciones de Gate en toda la aplicación - no solo las comprobaciones del panel de Clearance. Esto afecta a cada `can()`, `Gate::allows()`, y comprobación de política en tu aplicación, no solo a las que Clearance define. Para un alcance más limitado, déjalo desactivado y otorga permisos explícitamente en su lugar.
:::

## Detección de alias

Si tu base de datos ya contiene un rol llamado `super_admin`, `superadmin`, `super-admin`, `root`, u `owner`, el instalador lo detecta y te solicita interactivamente promoverlo o crear un `super_admin` independiente. Usa `--super-admin-role=NAME` para omitir el prompt:

```bash
php artisan clearance:install --super-admin-role=owner
```

::: tip La promoción no es destructiva
La promoción renombra el rol en su lugar - todas las asignaciones `model_has_roles` existentes se preservan vía la FK en `role_id`. Ningún usuario pierde su asignación de rol existente durante la promoción.
:::

## Acceso al panel y permisos de escritura granulares

Navega a `/clearance` tras la instalación. El acceso de lectura a todo el panel requiere el permiso `clearance-access`, verificado vía `can()`, nunca `hasRole()`:

```php
$user->givePermissionTo('clearance-access');
```

Las operaciones de escritura en cada sección están controladas por un permiso dedicado y delegable - todos creados por `clearance:install` y asignados a `super_admin`:

| Permiso | Controla |
|---|---|
| `clearance-permissions-write` | Crear / editar / eliminar permisos |
| `clearance-roles-write` | Crear / editar / eliminar roles y sus asignaciones de permisos |
| `clearance-guards-write` | Crear / editar / eliminar guards |
| `clearance-settings-write` | Editar metadatos de visualización, iconos, colores, rol predeterminado |
| `clearance-users-write` | Asignar / eliminar roles para un usuario específico |

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

::: tip Otorgar acceso limitado
Combina `clearance-access` con exactamente un permiso `clearance-{section}-write` para dar a un usuario (digamos, un líder de equipo) acceso de escritura a una sola sección del panel - roles, por ejemplo - sin exponer configuración, guards, o gestión de usuarios.
:::

Si una fila de permiso granular no existe en absoluto en la base de datos (un estado heredado previo a la siembra), la comprobación recurre a `clearance-access` por compatibilidad hacia atrás. En cualquier instancia que haya ejecutado `clearance:install`, este fallback nunca se activa.

## Permisos `clearance-*` reservados

Los permisos `clearance-*` están reservados: no pueden crearse, renombrarse, eliminarse, ni añadirse a ningún rol excepto a través del propio flujo de instalación del paquete - ni siquiera por un actor `super_admin` para el propio rol `super_admin`.

## Próximos pasos

- [Permisos](/es/features/permissions) - el panel general de CRUD de permisos (excluye `clearance-*`).
- [Roles](/es/features/roles) - asignar permisos `clearance-*-write` a un rol personalizado.
- [Seguridad](/es/guide/security) - el modelo completo de escalada de privilegios y techos.
