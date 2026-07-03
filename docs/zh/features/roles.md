# 角色

角色面板是构建在 Spatie 的 `Role` 模型之上的完整 CRUD 界面，并扩展了两个 Clearance 特有的概念：**作用域**（全局 vs. 上下文）和**上限**（一个用于限定子角色权限范围的父角色）。

## 该面板的功能

- **创建/编辑/删除**角色。
- **看守器限定的权限分配**——将权限分配给某个角色，并按该角色的看守器进行过滤。
- **作用域**选择——`global` 或 `contextual`。
- **上限**配置——设置或移除一个父角色。

<Screenshot src="/screenshots/roles-page.png" alt="Clearance 角色面板列出角色及其作用域和上限" caption="角色面板列出每一个角色及其作用域，以及（如果已设置）其上限父角色。" />

## 作用域：全局 vs. 上下文

每个角色在创建时都要声明一个作用域：

- **`global`**——通过 `$user->assignRole()` 直接分配给用户，全局生效。
- **`contextual`**——绑定到一个或多个模型类型（`context_types`），只能通过 `UserRoleContext` 在特定的上下文实例内分配。参见[上下文授权](/zh/features/contextual-authorization)和[用户](/zh/features/users#上下文角色的底层实现)。

没有明确 `RoleMeta` 记录的角色，出于向后兼容默认为 `global`。

## 上限角色

一个角色可以声明一个上限——一个父角色，其权限集合作为上限：

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
```

<Screenshot src="/screenshots/role-form-ceiling.png" alt="角色表单中选择了一个上限父角色" caption="在角色表单（RoleForm）中设置一个上限父角色。" />

每次保存时自动强制执行以下规则：

- 子角色只能持有父角色同样持有的权限。任何超出的权限都会在 `setParent()` 以及此后每一次权限同步时被默默裁剪——没有异常，也没有错误提示，只是被静默强制执行。
- 一个已经作为父角色的角色不能成为子角色（`ClearanceScopeViolationException`）。
- 一个已经是子角色的角色本身不能成为父角色（`ClearanceScopeViolationException`）。
- 从父角色移除权限会自动级联到每一个子角色。

::: warning 上限只会裁剪权限，从不授予权限
设置一个权限宽松的父角色本身不会给子角色带来任何权限。上限严格来说只是一个上界——它从不给子角色增加权限，只能移除。
:::

这可以在角色面板（`RoleForm`）中按角色配置，也可以通过 `RoleService::setParent()`/`RoleService::removeParent()` 以编程方式配置。

## 访问控制

读访问需要 `clearance-access`。创建、编辑或删除一个角色——包括其权限分配、作用域和上限——需要 `clearance-roles-write`：

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

## 路由与嵌入式组件

| URI | 路由名称 | 说明 |
|---|---|---|
| `/clearance/roles` | `clearance.roles` | 角色 CRUD，带看守器限定的权限分配、作用域（全局/上下文）和上限 |

```blade
<livewire:clearance::roles.role-manager />
```

## 数据库

角色元数据（作用域、上限、锁定状态）存放在 `clr_role_meta` 中，而不是 Spatie 的 `roles` 表：

| 数据表 | 关键列 |
|---|---|
| `clr_role_meta` | `role_id`（唯一，外键 -> `roles`）——`is_locked`、`scope`（`global`\|`contextual`）、`context_types`（json）——`parent_role_id`（可空外键 -> `roles`，`ON DELETE SET NULL`） |

## 下一步

- [上下文授权](/zh/features/contextual-authorization)——`scope=contextual` 的角色在运行时是如何检查的。
- [用户](/zh/features/users)——为特定用户分配角色，无论是全局还是按上下文。
- [服务参考](/zh/api/services)——完整的 `RoleService` API。
