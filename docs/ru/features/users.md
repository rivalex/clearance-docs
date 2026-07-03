# Пользователи

Модуль пользователей - это панель для отдельного пользователя, предназначенная для назначения ролей - глобально или в рамках конкретного контекста - а также для выдачи или запрета отдельных разрешений в рамках контекста. Он опционален и по умолчанию отключён.

## Включение модуля

```php
// config/clearance.php
'modules' => ['users' => true],
```

## Что делает панель

- **Глобальное назначение роли** - назначение ролей с `scope=global` пользователю напрямую, во всём приложении.
- **Контекстное назначение роли** - назначение ролей с `scope=contextual`, привязанных к конкретному экземпляру модели (Store, Team или Project, объявленных в `contextual_models`).
- **Переопределения разрешений по контексту** - для каждого контекстного назначения роли дополнительные разрешения могут быть принудительно выданы или запрещены независимо для каждого экземпляра контекста.
- **Удаление назначения** - удаляет роль у пользователя (глобальной или контекстной области), каскадно удаляя все связанные переопределения.

<Screenshot src="/screenshots/user-panel-global.png" alt="Панель пользователя с глобальным назначением роли" caption="Назначение глобальной роли пользователю - применяется во всём приложении." />

<Screenshot src="/screenshots/user-panel-contextual.png" alt="Панель пользователя с контекстным назначением роли и переопределениями разрешений по контексту" caption="Назначение контекстной роли и настройка переопределений разрешений по контексту для конкретного экземпляра Store." />

::: warning Ограничение по привилегиям при изменении самого себя
И назначение ролей, и прямая/переопределяющая выдача разрешений применяют ограничение по привилегиям: актор, не являющийся `super_admin`, не может изменять собственные назначения и не может выдать разрешение, которым не обладает сам.
:::

## Контроль доступа

Доступ к `/clearance/user/{userId}` требует `clearance-access` (чтение) и `clearance-users-write` (назначение/удаление):

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-users-write');
```

## Маршрут и встроенный компонент

| URI | Имя маршрута | Описание |
|---|---|---|
| `/clearance/user/{userId}` | `clearance.user` | Назначение ролей для отдельного пользователя + контекстные переопределения разрешений (требует `modules.users = true`) |

```blade
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

### Ссылка на панель из вашего приложения

```php
route('clearance.user', ['userId' => $user->id])
```

## Контекстные роли изнутри

Назначение контекстной роли пользователю создаёт строку `UserRoleContext`:

```php
use Rivalex\Clearance\Models\UserRoleContext;

// Samuele is "StoreStaff" only within Alessandro's Store
UserRoleContext::create([
    'user_id'      => $samuele->id,
    'role_id'      => $staffRole->id,
    'context_type' => Store::class,
    'context_id'   => $store->id,
]);
```

Проверки авторизации учитывают контекст через `$user->canIn()`, `@canin` и `ContextService`. См. [Контекстную авторизацию](/ru/features/contextual-authorization) для полной модели разрешения (resolution).

## База данных

| Таблица | Ключевые столбцы |
|---|---|
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - уникальны по `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) |

## Следующие шаги

- [Контекстная авторизация](/ru/features/contextual-authorization) - полный порядок приоритета разрешения для `canIn()`.
- [Роли](/ru/features/roles) - настройка области роли и `context_types`.
- [Справочник конфигурации](/ru/guide/configuration) - `modules.users`, `contextual_models`.
