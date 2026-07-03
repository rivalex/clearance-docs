# Migrar desde una instalación de Spatie existente

Si tu aplicación ya usa `spatie/laravel-permission` con roles, permisos y asignaciones existentes, `clearance:install` los detecta y los preserva - nunca ejecuta las migraciones de Spatie si la tabla `roles` ya existe. Consulta [Instalación](/es/guide/installation) para el flujo completo de instalación.

Una vez instalado, usa `clearance:backfill` para adoptar los propios metadatos de Clearance sobre tus roles y guards existentes.

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

## Qué hace cada sección

| Sección | Efecto |
|---|---|
| `meta` | Genera un nombre de visualización en `clr_meta` (nombre del rol en formato título) para cada rol que no tenga uno. |
| `roles` | Genera valores predeterminados en `clr_role_meta` (`scope=global`, `is_locked=false`) para cada rol que no tenga uno. |
| `guards` | Importa guards desde `config('auth.guards')` a `clr_guards`, filtrados por `allowed_guard_drivers`. |

Usa `--only=<section>` para ejecutar una sola sección en lugar de las tres.

::: tip Idempotente
Todas las secciones son idempotentes - volver a ejecutar `clearance:backfill` es seguro y no duplicará ni sobrescribirá filas `clr_*` existentes.
:::

## Casos especiales

El repositorio del paquete tiene una guía de migración más detallada que cubre casos especiales no resumidos aquí - en particular qué ocurre cuando tu base de datos ya tiene un rol llamado `super_admin` (o un alias como `superadmin`, `root`, `owner`) que entra en conflicto con el propio rol `super_admin` de Clearance, y notas sobre subclases de modelo `Role`/`Permission` personalizadas y el comportamiento de importación de guards. Léela antes de hacer un backfill sobre una base de datos de producción con datos de rol existentes no triviales. Consulta también [Super Admin](/es/features/super-admin) para el comportamiento de detección de alias durante `clearance:install`.

## Relacionado

- [Instalación](/es/guide/installation) - el flujo completo de `clearance:install`
- [Comandos Artisan](/es/api/artisan-commands) - referencia completa de comandos
- [Arquitectura](/es/guide/architecture) - las tablas `clr_*` que el backfill rellena
