# Instalación

## Requisitos

Consulta la [tabla de requisitos](/es/guide/introduction#requirements) completa en la página de Introducción - PHP ^8.3, Laravel ^11.0|^12.0|^13.0, spatie/laravel-permission ^6.0, livewire/livewire ^3.0|^4.0, livewire/flux ^2.14.

## Instalar el paquete

```bash
composer require rivalex/clearance
```

## Ejecutar el instalador

```bash
php artisan clearance:install
```

::: danger Ejecuta siempre clearance:install, nunca un migrate simple
Las propias migraciones de Clearance dependen de que existan primero las tablas `roles`/`permissions` de Spatie; el instalador publica y ejecuta las migraciones de Spatie antes que las suyas. Si ejecutas `php artisan migrate` directamente en una instalación nueva y Spatie aún no está configurado, obtendrás un mensaje de error accionable indicándote que ejecutes el instalador en su lugar.
:::

### Flags opcionales

| Flag | Descripción |
|---|---|
| `--user=ID` | Asigna el rol `super_admin` a un usuario tras la instalación |
| `--role=NAME` | Asigna `clearance-access` a un rol (se crea si no existe) |
| `--super-admin-role=NAME` | Promueve un rol existente a `super_admin` (omite el prompt interactivo) |
| `--force` | Vuelve a ejecutar aunque ya esté instalado |

### Qué hace el instalador

1. Publica `config/clearance.php`.
2. Publica las migraciones de Clearance.
3. Detecta las tablas de Spatie Permission - si la tabla `roles` no existe, publica y ejecuta primero las migraciones de Spatie automáticamente.
4. Ejecuta todas las migraciones pendientes.
5. Crea `clearance-access` y los 5 permisos de capacidad granulares: `clearance-permissions-write`, `clearance-roles-write`, `clearance-guards-write`, `clearance-settings-write`, `clearance-users-write`.
6. Crea el rol `super_admin` con todos los permisos `clearance-*` asignados (aditivo - nunca elimina permisos existentes al volver a ejecutarse).
7. Escribe `storage/.clearance-installed` como marcador de idempotencia.

::: tip ¿Migrando desde una instalación de Spatie existente?
Usa `php artisan clearance:backfill` en lugar de empezar desde cero, y lee primero la [guía de Migración](/es/guide/migration) - cubre la colisión con `super_admin`, subclases de modelo personalizadas, e importación de guards.
:::

## Añade HasClearance a tu modelo User

Para usar los métodos de autorización contextual (`$user->canIn()`, `$user->hasRoleIn()`), añade el trait `HasClearance` a `App\Models\User`. Ya incluye el `HasRoles` de Spatie - una sola línea reemplaza a ambos:

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance; // includes HasRoles + all contextual authorization methods
}
```

::: tip ¿Ya usas `use HasRoles`?
Reemplázalo con `use HasClearance`, o conserva ambos - PHP deduplica automáticamente la composición de traits, sin conflictos.
:::

Sin este trait, `$user->canIn()` y los métodos relacionados no están disponibles, pero las directivas Blade (`@canin`, `@hasrolein`) y `ContextService` siguen funcionando de todos modos - no dependen del trait. Consulta [Autorización Contextual](/es/features/contextual-authorization) para más detalles.

## Próximos pasos

Continúa con el [Inicio Rápido](/es/guide/quick-start) para montar el panel y empezar a gestionar permisos.
