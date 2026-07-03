# Comandos Artisan

Clearance incluye dos comandos Artisan: `clearance:install` para la configuración inicial, y `clearance:backfill` para sembrar valores predeterminados en instalaciones existentes. Consulta la [guía de Instalación](/es/guide/installation.md) para el recorrido completo de la configuración.

## `clearance:install`

```bash
php artisan clearance:install
php artisan clearance:install --user=1
php artisan clearance:install --role=editor
php artisan clearance:install --super-admin-role=owner
php artisan clearance:install --force
```

| Flag | Descripción |
|---|---|
| `--user=ID` | Asigna el rol `super_admin` a un usuario tras la instalación. |
| `--role=NAME` | Asigna `clearance-access` a un rol (se crea si no existe). |
| `--super-admin-role=NAME` | Promueve un rol existente a `super_admin` (omite el prompt interactivo). |
| `--force` | Vuelve a ejecutar aunque ya esté instalado. |

### Qué hace, en orden

1. Publica `config/clearance.php`.
2. Publica las migraciones de Clearance.
3. Detecta las tablas de Spatie Permission — si la tabla `roles` no existe, publica y ejecuta primero las migraciones de Spatie automáticamente.
4. Ejecuta todas las migraciones pendientes.
5. Crea `clearance-access` y los 5 permisos de capacidad granulares: `clearance-permissions-write`, `clearance-roles-write`, `clearance-guards-write`, `clearance-settings-write`, `clearance-users-write`.
6. Crea el rol `super_admin` con todos los permisos `clearance-*` asignados. Este paso es aditivo — nunca elimina permisos existentes al volver a ejecutarse.
7. Escribe `storage/.clearance-installed` como marcador de idempotencia.

### Detección de alias

Si la base de datos ya contiene un rol llamado `super_admin`, `superadmin`, `super-admin`, `root`, u `owner`, el instalador lo detecta y solicita interactivamente promoverlo o crear un rol `super_admin` independiente. Usa `--super-admin-role=NAME` para omitir el prompt de forma no interactiva.

La promoción renombra el rol en su lugar — todas las asignaciones `model_has_roles` se preservan vía la clave foránea en `role_id`.

::: warning
Los permisos `clearance-*` están protegidos fuera del flujo de instalación — consulta [`PermissionService`](/es/api/services.md#permissionservice) y `ClearanceProtectedResourceException` en [Servicios](/es/api/services.md#exceptions).
:::

## `clearance:backfill`

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

| Flag | Descripción |
|---|---|
| `--only=meta\|roles\|guards` | Ejecuta solo una sección de backfill. |
| `--dry-run` | Previsualiza los cambios sin escribir. |

### Secciones

| Sección | Descripción |
|---|---|
| `meta` | Genera un nombre de visualización en `clr_meta` (nombre del rol en formato título) para cada rol que no tenga uno. |
| `roles` | Genera valores predeterminados en `clr_role_meta` (`scope=global`, `is_locked=false`) para cada rol que no tenga uno. |
| `guards` | Importa guards desde `config('auth.guards')` a `clr_guards`, filtrados por `allowed_guard_drivers`. |

Todas las secciones son idempotentes — ejecutar `clearance:backfill` repetidamente es seguro y solo rellena lo que falta.

::: tip
Para instalaciones de Spatie preexistentes: `clearance:install` nunca ejecuta las migraciones de Spatie si la tabla `roles` ya existe, así que los roles, permisos, y asignaciones existentes se preservan automáticamente.
:::

## Ver también

- [Servicios](/es/api/services.md) — `GuardService` y `MetaService`, que respaldan las secciones de backfill `guards` y `meta`.
- [Guía de Migración](/es/guide/migration.md) — migrar una instalación existente de Spatie Permission a Clearance.
