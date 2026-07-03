# Контекстная авторизация

Clearance расширяет глобальную модель авторизации Spatie проверками, привязанными к контексту. Роли пользователя могут быть назначены в рамках конкретного контекста (например, Store, Tenant или Project) и невидимы вне этой области. Это ключевой механизм, лежащий в основе [модуля пользователей](/ru/features/users) и контекстных [Ролей](/ru/features/roles#scope-global-vs-contextual).

## В модели User

Доступно, когда ваша модель `User` использует трейт `HasClearance` (см. [Установку](/ru/guide/installation#add-hasclearance-to-your-user-model)):

```php
// Does the user have this permission in this context?
$user->canIn('orders-create', $store);

// Alias - mirrors Spatie's hasPermissionTo() naming
$user->hasPermissionIn('orders-create', $store);

// Does the user hold this role in this context?
$user->hasRoleIn('manager', $store);

// Optionally filter by guard
$user->canIn('orders-create', $store, 'api');
$user->hasRoleIn('manager', $store, 'web');
```

## В представлениях Blade

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

::: tip Безопасно для гостя по дизайну
Обе директивы безопасно разрешаются (возвращают `false`) для гостевого пользователя - они никогда не выбрасывают исключение. Вы можете использовать `@canin`/`@hasrolein` в представлениях, рендерящихся до гарантированной аутентификации, не оборачивая их в проверку `@auth`.
:::

## Через ContextService

Для использования в контроллерах, политиках или заданиях (jobs), где модель `User` может не иметь трейта `HasClearance`:

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

## Через фасад Clearance

Фасад `Clearance` делегирует тому же сервису - удобная глобальная точка входа:

```php
Clearance::canIn($user, 'orders-create', $store);
Clearance::resolveFor($user, $store);
Clearance::guards(); // names of all managed authentication guards
```

См. [справочник фасада](/ru/api/facade) для полного списка методов.

## Как работает разрешение (resolution)

`resolveFor()` объединяет три источника в следующем порядке приоритета:

1. **Контекстные выдачи через роли** - разрешения от ролей, которыми пользователь обладает именно в этом контексте (`clr_role_ctx`).
2. **Глобальные выдачи через роли** - разрешения от глобально назначенных пользователю ролей Spatie (так `super_admin` и другие глобальные роли удовлетворяют `canIn()` для любого контекста).
3. **Переопределения по контексту** (`clr_ctx_overrides`) - `forced_on` добавляет разрешение поверх вышеперечисленного; `forced_off` убирает его, даже если разрешение было выдано глобальной ролью.

::: danger Запрет всегда побеждает
Если разрешение имеет статус `forced_off` для пользователя в данном контексте, этот запрет перекрывает все остальные источники - включая выдачу глобальной ролью и контекстную выдачу через роль. Не существует способа повторно выдать разрешение `forced_off` для этого контекста иначе как удалив само переопределение. Проектируйте свою политику переопределений с учётом этого: `forced_off` - самый сильный сигнал в системе.
:::

## База данных

| Таблица | Ключевые столбцы |
|---|---|
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - уникальны по `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) |

## Следующие шаги

- [Пользователи](/ru/features/users) - UI панели для назначения контекстных ролей и переопределений.
- [Роли](/ru/features/roles#scope-global-vs-contextual) - объявление роли как `contextual` и её допустимые `context_types`.
- [Справочник трейтов и директив](/ru/api/traits-directives) - полный трейт `HasClearance` и сигнатуры директив Blade.
- [Справочник сервисов](/ru/api/services) - полный API `ContextService`.
