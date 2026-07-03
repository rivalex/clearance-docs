# Фасад

Фасад `Clearance` (`Rivalex\Clearance\Facades\Clearance`) предоставляет API контекстной авторизации пакета в виде статических вызовов. За ним стоит `src/Clearance.php`, который делегирует каждый вызов [`ContextService`](/ru/api/services.md#contextservice).

```php
use Rivalex\Clearance\Facades\Clearance;
```

::: tip
Директивы Blade и `ContextService` работают без каких-либо изменений в вашей модели `User`. Фасад - это удобная обёртка вокруг того же движка - см. [Контекстную авторизацию](/ru/features/contextual-authorization.md) для понимания концепции проверок, привязанных к контексту.
:::

## `canIn()`

Проверяет, обладает ли пользователь разрешением, привязанным к экземпляру контекста.

```php
Clearance::canIn($user, 'orders-create', $store);      // bool
Clearance::canIn($user, 'orders-create', $store, 'api'); // optional 3rd arg: guard name filter
```

| Параметр | Тип | Описание |
|---|---|---|
| `$user` | `Authenticatable` | Проверяемый пользователь. |
| `string $permission` | `string` | Имя разрешения, напр. `orders-create`. |
| `$context` | экземпляр модели | Контекст, к которому привязано разрешение, напр. `Store`. |
| `?string $guard` | `string\|null` | Опциональный фильтр по имени guard'а. |

Возвращает `bool`.

## `hasPermissionIn()`

Алиас [`canIn()`](#canin), названный в соответствии с соглашением Spatie `hasPermissionTo()`.

```php
Clearance::hasPermissionIn($user, 'orders-create', $store);
```

Возвращает `bool`.

## `hasRoleIn()`

Проверяет, обладает ли пользователь ролью, привязанной к экземпляру контекста.

```php
Clearance::hasRoleIn($user, 'manager', $store);
```

Возвращает `bool`.

## `resolveFor()`

Разрешает все действующие имена разрешений, которыми обладает пользователь в данном контексте.

```php
Clearance::resolveFor($user, $store); // Collection<string>
```

Возвращает `Collection<string>` с именами разрешений. См. [`ContextService::resolveFor()`](/ru/api/services.md#contextservice) для правил приоритета из 3 источников, используемых при построении этого списка.

## `guards()`

Перечисляет имена всех guard'ов аутентификации, управляемых Clearance.

```php
Clearance::guards(); // Collection<string>
```

Возвращает `Collection<string>`. За этим стоит [`GuardService`](/ru/api/services.md#guardservice). См. [страницу функции Guards](/ru/features/guards.md) о том, как guard'ы настраиваются и внедряются в `auth.guards`.

## Смотрите также

- [Трейты и директивы](/ru/api/traits-directives.md) - эквивалентные методы экземпляра через `HasClearance` и безопасные для гостя директивы Blade.
- [Сервисы](/ru/api/services.md) - лежащий в основе `ContextService` и связанные сервисы.
