# 权限

权限面板是构建在 Spatie 的 `Permission` 模型之上的完整 CRUD 界面。Clearance 从不引入自己的权限数据表——你在这里创建的每一条权限都是一条常规的 Spatie 权限，可以在任何使用 Spatie API 的地方使用（`$user->can()`、`Gate::allows()`、策略、`@can`）。

## 该面板的功能

- **创建**——新增一条权限，会依据[命名约定](#权限命名约定)进行校验（除非已禁用），并限定到某个看守器。
- **编辑**——重命名权限或更改其看守器。
- **删除**——移除权限及其角色/用户分配关系（标准的 Spatie 级联行为）。

<Screenshot src="/screenshots/permissions-page.png" alt="Clearance 权限面板按命名前缀列出权限" caption="权限面板按命名前缀列出每一条 Spatie 权限。" />

<Screenshot src="/screenshots/permission-form-modal.png" alt="带命名约定校验的权限创建/编辑表单" caption="权限表单在保存前会校验 group-action 命名约定。" />

## 权限命名约定

权限必须遵循 `group-action` 格式：

```
orders-create       correct
orders-read         correct
store-orders-delete correct

create              wrong (bare action, no group)
orders.create       wrong (dot separator)
Orders-Create       wrong (camelCase)
```

组前缀（`orders-create` 中的 `orders`）正是[仪表板](/zh/features/dashboard)用来统计不同权限组数量的依据。

::: tip 禁用约束
有些团队是从另一套命名方案迁移过来的。可以按环境禁用该检查：

```php
// config/clearance.php
'enforce_naming_convention' => false,
```
:::

## 访问控制

对该面板的读访问（查看权限列表）需要 `clearance-access`。创建、编辑或删除权限还额外需要 `clearance-permissions-write`：

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-permissions-write');
```

::: warning `clearance-*` 权限是被保留的
`clearance-*` 权限本身（`clearance-access`、`clearance-permissions-write` 等）无法通过此面板创建、重命名或删除——即使是 `super_admin` 本身的操作者也不例外。它们只由该包自身的安装流程管理。参见[超级管理员](/zh/features/super-admin#受保护的-clearance-权限)。
:::

## 路由与嵌入式组件

| URI | 路由名称 | 说明 |
|---|---|---|
| `/clearance/permissions` | `clearance.permissions` | 权限 CRUD，带分组前缀命名校验 |

```blade
<livewire:clearance::permissions.permission-manager />
```

## 下一步

- [角色](/zh/features/roles)——将权限分配给某个角色，并限定到该角色的看守器。
- [超级管理员](/zh/features/super-admin)——完整的精细化写权限表。
- [配置参考](/zh/guide/configuration)——`enforce_naming_convention`、`naming_separator`。
