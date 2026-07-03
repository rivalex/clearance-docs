# Facade

`Clearance` facade（`Rivalex\Clearance\Facades\Clearance`）以静态调用的形式暴露该包的上下文授权 API。它由 `src/Clearance.php` 支撑，后者将每一次调用都委托给 [`ContextService`](/zh/api/services.md#contextservice)。

```php
use Rivalex\Clearance\Facades\Clearance;
```

::: tip
Blade 指令和 `ContextService` 无需对你的 `User` 模型做任何改动即可使用。该 facade 只是围绕同一套引擎的一层便捷封装——上下文限定检查背后的概念请参见[上下文授权](/zh/features/contextual-authorization.md)。
:::

## `canIn()`

检查用户是否持有限定在某个上下文实例范围内的权限。

```php
Clearance::canIn($user, 'orders-create', $store);      // bool
Clearance::canIn($user, 'orders-create', $store, 'api'); // optional 3rd arg: guard name filter
```

| 参数 | 类型 | 说明 |
|---|---|---|
| `$user` | `Authenticatable` | 要检查的用户。 |
| `string $permission` | `string` | 权限名称，例如 `orders-create`。 |
| `$context` | 模型实例 | 该权限所限定的上下文，例如一个 `Store`。 |
| `?string $guard` | `string\|null` | 可选的看守器名称过滤器。 |

返回 `bool`。

## `hasPermissionIn()`

[`canIn()`](#canin) 的别名，命名上呼应 Spatie 的 `hasPermissionTo()` 约定。

```php
Clearance::hasPermissionIn($user, 'orders-create', $store);
```

返回 `bool`。

## `hasRoleIn()`

检查用户是否持有限定在某个上下文实例范围内的角色。

```php
Clearance::hasRoleIn($user, 'manager', $store);
```

返回 `bool`。

## `resolveFor()`

解析用户在给定上下文内持有的每一个生效权限的名称。

```php
Clearance::resolveFor($user, $store); // Collection<string>
```

返回一个 `Collection<string>` 权限名称集合。构建该列表所使用的三来源优先级规则，请参见 [`ContextService::resolveFor()`](/zh/api/services.md#contextservice)。

## `guards()`

列出由 Clearance 管理的所有认证看守器的名称。

```php
Clearance::guards(); // Collection<string>
```

返回一个 `Collection<string>`。由 [`GuardService`](/zh/api/services.md#guardservice) 支撑。看守器的配置和注入 `auth.guards` 的方式，请参见[看守器功能页面](/zh/features/guards.md)。

## 另请参阅

- [Trait 与指令](/zh/api/traits-directives.md)——通过 `HasClearance` 提供的等效实例方法，以及对访客安全的 Blade 指令。
- [服务](/zh/api/services.md)——底层的 `ContextService` 及相关服务。
