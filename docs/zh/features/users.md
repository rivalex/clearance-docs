# 用户

用户模块是一个按用户提供的面板，用于分配角色——无论是全局还是限定于特定上下文——以及在某个上下文内单独授予或拒绝个别权限。它是可选启用的，默认禁用。

## 启用该模块

```php
// config/clearance.php
'modules' => ['users' => true],
```

## 该面板的功能

- **全局角色分配**——将 `scope=global` 的角色直接分配给该用户，全应用生效。
- **上下文角色分配**——将 `scope=contextual` 的角色分配给限定在特定模型实例（在 `contextual_models` 中声明的一个 Store、Team 或 Project）范围内。
- **按上下文的权限覆盖**——针对每一个上下文角色分配，可以按每个上下文实例单独强制授予或强制拒绝额外的权限。
- **移除分配**——从用户身上移除一个角色（全局或上下文作用域），并级联删除所有关联的覆盖。

<Screenshot src="/screenshots/user-panel-global.png" alt="用户面板显示全局角色分配" caption="为用户分配一个全局角色——全应用范围生效。" />

<Screenshot src="/screenshots/user-panel-contextual.png" alt="用户面板显示上下文角色分配及按上下文的权限覆盖" caption="为特定 Store 实例分配一个上下文角色，并设置按上下文的权限覆盖。" />

::: warning 自我修改上的权限提升上限
角色分配以及直接/覆盖式的权限授予都强制执行一个权限提升上限：非 `super_admin` 的操作者无法修改自己的分配，也无法授予自己都不持有的权限。
:::

## 访问控制

访问 `/clearance/user/{userId}` 需要 `clearance-access`（读取）和 `clearance-users-write`（分配/移除）：

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-users-write');
```

## 路由与嵌入式组件

| URI | 路由名称 | 说明 |
|---|---|---|
| `/clearance/user/{userId}` | `clearance.user` | 按用户分配角色 + 上下文权限覆盖（需要 `modules.users = true`） |

```blade
{{-- 用户模块（配置中需要 modules.users = true） --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

### 从你的应用链接到该面板

```php
route('clearance.user', ['userId' => $user->id])
```

## 上下文角色的底层实现

为用户分配一个上下文角色会创建一条 `UserRoleContext` 记录：

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

授权检查会通过 `$user->canIn()`、`@canin` 和 `ContextService` 遵循该上下文。完整的解析模型请参见[上下文授权](/zh/features/contextual-authorization)。

## 数据库

| 数据表 | 关键列 |
|---|---|
| `clr_role_ctx` | `user_id`、`role_id`（外键 -> `roles`）、`context_type`、`context_id`——按 `(user, role, context_type, context_id)` 联合唯一 |
| `clr_ctx_overrides` | `user_id`、`role_id`（外键 -> `roles`）、`permission_id`（外键 -> `permissions`）、`context_type`、`context_id`、`type`（`forced_on`\|`forced_off`） |

## 下一步

- [上下文授权](/zh/features/contextual-authorization)——`canIn()` 的完整解析优先级。
- [角色](/zh/features/roles)——配置一个角色的作用域和 `context_types`。
- [配置参考](/zh/guide/configuration)——`modules.users`、`contextual_models`。
