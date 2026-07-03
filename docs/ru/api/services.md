# Сервисы

Все сервисы Clearance находятся в `src/Services/` и разрешаются из контейнера, напр. `app(ContextService::class)`. Это слой, к которому стоит обращаться из контроллеров, политик и заданий (jobs) - везде, где модель `User` может не использовать трейт `HasClearance`.

## `ContextService`

`Rivalex\Clearance\Services\ContextService` (`src/Services/ContextService.php`) - это движок, стоящий за контекстной авторизацией. И [фасад `Clearance`](/ru/api/facade.md), и трейт `HasClearance` делегируют ему.

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

`resolveFor()` строит результат по приоритету из 3 источников:

1. Контекстные выдачи через роли
2. Глобальные выдачи через роли
3. Переопределения по контексту (`forced_on` добавляет, `forced_off` всегда побеждает)

См. [Контекстную авторизацию](/ru/features/contextual-authorization.md) для концептуального разбора этого приоритета.

## `RoleService`

`Rivalex\Clearance\Services\RoleService` (`src/Services/RoleService.php`) управляет ceiling-ролями.

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
app(RoleService::class)->removeParent($childRole);
```

| Метод | Описание |
|---|---|
| `setParent($childRole, $parentRole)` | Устанавливает ceiling: избыточные разрешения на `$childRole` сверх набора родителя незаметно обрезаются. Выбрасывает `ClearanceScopeViolationException`, если дочерняя роль уже является родителем, или родитель уже является дочерней ролью. |
| `removeParent($childRole)` | Удаляет связь ceiling. |

::: warning
Ceiling только обрезает - но никогда не выдаёт. Разрешительная родительская роль сама по себе не даёт дочерней роли никаких разрешений. Удаление разрешения у родительской роли автоматически каскадно распространяется на каждую дочернюю роль.
:::

См. [Роли](/ru/features/roles.md) для более глубокого разбора понятия ceiling-роли.

## `PermissionService`

`Rivalex\Clearance\Services\PermissionService` (`src/Services/PermissionService.php`) обеспечивает CRUD панели Разрешений.

Он применяет соглашение об именовании `group-action`, контролируемое ключами конфигурации `enforce_naming_convention` и `naming_separator`, и защищает пространство имён разрешений `clearance-*` от создания, переименования или удаления вне процесса установки.

## `GuardService`

`Rivalex\Clearance\Services\GuardService` (`src/Services/GuardService.php`) обеспечивает CRUD панели Guards.

Он внедряет guard'ы, хранящиеся в БД, в `auth.guards` при загрузке, отфильтрованные по ключу конфигурации `allowed_guard_drivers` - драйвер вне списка пропускается с предупреждением в лог, а не приводит к повреждению конфигурации аутентификации.

## `MetaService`

`Rivalex\Clearance\Services\MetaService` (`src/Services/MetaService.php`) читает и записывает `clr_meta` (отображаемое имя, описание, санитизированную SVG-иконку, цвет) для субъекта-роли или guard'а. Он обеспечивает работу редактора метаданных отображения в панели Настроек.

SVG-иконки проходят через `Rivalex\Clearance\Support\SvgSanitizer` (санитайзер на основе allow-list) как перед сохранением, так и при рендеринге.

## `UserClearanceService`

`Rivalex\Clearance\Services\UserClearanceService` (`src/Services/UserClearanceService.php`) обеспечивает работу панели модуля пользователей:

- Глобальное назначение роли
- Контекстное назначение роли (через `UserRoleContext`)
- Переопределения разрешений по контексту (через `UserContextPermissionOverride`, `forced_on` / `forced_off`)
- Удаление назначений, каскадно удаляющее переопределения

Он применяет ограничение по привилегиям: актор, не являющийся `super_admin`, не может изменять собственные назначения и не может выдать разрешение, которым не обладает сам. См. [Super Admin](/ru/features/super-admin.md) и [Пользователи](/ru/features/users.md).

## Исключения

Все находятся в `src/Exceptions/`:

| Исключение | Выбрасывается когда |
|---|---|
| `ClearanceConfigException` | Читается некорректное или неправильно настроенное значение `config/clearance.php` (напр. неверный `naming_separator`). |
| `ClearanceNamingException` | Имя разрешения нарушает соглашение об именовании `group-action` при включённом принудительном соблюдении. |
| `ClearanceProtectedResourceException` | Предпринимается попытка изменить защищённое разрешение `clearance-*` или заблокированный ресурс вне процесса установки. |
| `ClearanceScopeViolationException` | Некорректная связь ceiling-роли (родитель пытается стать дочерней ролью или наоборот) или несовпадение области. |

## Смотрите также

- [Фасад](/ru/api/facade.md) - обёртка в виде статических вызовов вокруг `ContextService`.
- [Трейты и директивы](/ru/api/traits-directives.md) - эквиваленты в виде методов экземпляра и директив Blade.
- [Команды Artisan](/ru/api/artisan-commands.md) - `clearance:install` и `clearance:backfill`, которые формируют (seed) данные, с которыми работают эти сервисы.
