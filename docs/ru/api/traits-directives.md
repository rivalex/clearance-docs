# Трейты и директивы

Справочник по трейтам, concern'у, контракту и директивам Blade, составляющим публичный API Clearance для разработчика за пределами фасада и сервисов.

## `HasClearance`

`Rivalex\Clearance\Traits\HasClearance` - добавьте в вашу модель `User`.

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance;
}
```

Он включает `HasRoles` из Spatie внутри себя, так что этот единственный трейт заменяет оба - не добавляйте `Spatie\Permission\Traits\HasRoles` отдельно.

Он добавляет следующие методы экземпляра:

```php
$user->canIn('orders-create', $store);            // bool
$user->canIn('orders-create', $store, 'api');     // optional guard filter
$user->hasPermissionIn('orders-create', $store);  // alias of canIn()
$user->hasRoleIn('manager', $store);               // bool
$user->hasRoleIn('manager', $store, 'web');         // optional guard filter
```

| Метод | Возвращает | Описание |
|---|---|---|
| `canIn(string $permission, $context, ?string $guard = null)` | `bool` | Проверка разрешения, привязанная к экземпляру контекста. |
| `hasPermissionIn(string $permission, $context, ?string $guard = null)` | `bool` | Алиас `canIn()`. |
| `hasRoleIn(string $role, $context, ?string $guard = null)` | `bool` | Проверка роли, привязанная к экземпляру контекста. |

::: tip
Без этого трейта данные методы экземпляра недоступны - но директивы Blade и `ContextService` продолжают работать в любом случае. Ни один из них не зависит от трейта.
:::

## `HasPermissionGroups`

`Rivalex\Clearance\Concerns\HasPermissionGroups` - используйте, когда вам нужна собственная модель `Permission` (настроенная через `config('permission.models.permission')`) вместо собственной модели Clearance `Rivalex\Clearance\Models\Permission`.

Примените его, чтобы сохранить работу группированных UI-аксессоров, поддерживающих панель Разрешений, на вашей собственной модели:

```php
use Rivalex\Clearance\Concerns\HasPermissionGroups;

class Permission extends \Spatie\Permission\Models\Permission
{
    use HasPermissionGroups;
}
```

Он предоставляет следующие аксессоры:

| Аксессор / метод | Описание |
|---|---|
| `permission_group` | Префикс группы перед разделителем именования (напр. `orders` в `orders-create`). |
| `group_string` | Группа в виде строки для отображения. |
| `abilities()` | Набор способностей/действий, определённых для группы разрешения. |
| `colorForAbility()` | Цвет для способности разрешения, используемый UI панели. |

См. [Разрешения](/ru/features/permissions.md) о том, как соглашение об именовании и группировка используются в панели.

## `ClearanceContextable`

`Rivalex\Clearance\Contracts\ClearanceContextable` - реализуйте на модели контекста (напр. вашей модели `Store`), чтобы управлять тем, как она подписана в панели.

```php
interface ClearanceContextable
{
    public function clearanceLabel(): string;
}
```

Если модель контекста не реализует этот контракт, панель откатывается к `contextual_models.<FQCN>.label_attribute` (по умолчанию `name`) из `config/clearance.php`. См. [Конфигурацию](/ru/guide/configuration.md).

## Директивы Blade

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

| Директива | Эквивалентная проверка |
|---|---|
| `@canin($permission, $context)` | `ContextService::canIn()` |
| `@hasrolein($role, $context)` | `ContextService::hasRoleIn()` |

::: tip
Обе директивы безопасно разрешаются для гостевого пользователя - они возвращают `false` / пропускают блок и никогда не выбрасывают исключение. Они также не требуют трейта `HasClearance`; они разрешаются через тот же лежащий в основе `ContextService`, который использует фасад.
:::

## Смотрите также

- [Фасад](/ru/api/facade.md) - эквивалент этих методов экземпляра в виде статических вызовов.
- [Сервисы](/ru/api/services.md) - внутреннее устройство `ContextService` и порядок приоритета из 3 источников.
