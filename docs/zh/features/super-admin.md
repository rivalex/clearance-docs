# 超级管理员

`super_admin` 角色由 `clearance:install` 自动创建，并与所有 `clearance-*` 权限保持同步。

```bash
# Install and make user ID 1 a super admin
php artisan clearance:install --user=1
```

完整的安装程序参数列表请参见[安装指南](/zh/guide/installation)和 [Artisan 命令参考](/zh/api/artisan-commands)。

## Gate 绕过（可选启用）

`super_admin_gate_bypass` 控制 `super_admin` 角色是否通过一个 `Gate::before()` 钩子，在全局范围内绕过所有 Laravel 的 `can()`/`Gate::allows()`/策略检查。默认值：`false`。该绕过是可选启用的。要启用它：

```php
// config/clearance.php
'super_admin_gate_bypass' => true,
```

::: danger 应用范围内，而非仅限于面板范围
启用后，任何持有 `super_admin` 的用户都会跳过整个应用范围内的**所有** Gate 检查——不仅仅是 Clearance 面板的检查。这会影响你应用中每一个 `can()`、`Gate::allows()` 和策略检查，而不仅仅是 Clearance 定义的那些。若需要更窄的范围，请保持关闭，改为显式地授予权限。
:::

## 别名检测

如果你的数据库中已经存在一个名为 `super_admin`、`superadmin`、`super-admin`、`root` 或 `owner` 的角色，安装程序会检测到它，并交互式地提示你将其提升，或创建一个单独的 `super_admin`。使用 `--super-admin-role=NAME` 可以跳过该提示：

```bash
php artisan clearance:install --super-admin-role=owner
```

::: tip 提升是非破坏性的
提升会原地重命名该角色——所有现有的 `model_has_roles` 分配都会通过 `role_id` 上的外键被保留。在提升过程中，没有任何用户会丢失其现有的角色分配。
:::

## 面板访问与精细化的写权限

安装完成后访问 `/clearance`。对整个面板的读访问需要 `clearance-access` 权限，通过 `can()` 检查，绝不通过 `hasRole()`：

```php
$user->givePermissionTo('clearance-access');
```

每个分区的写操作都由一个专属的、可委派的权限控制——全部由 `clearance:install` 创建并分配给 `super_admin`：

| 权限 | 控制内容 |
|---|---|
| `clearance-permissions-write` | 创建/编辑/删除权限 |
| `clearance-roles-write` | 创建/编辑/删除角色及其权限分配 |
| `clearance-guards-write` | 创建/编辑/删除看守器 |
| `clearance-settings-write` | 编辑显示元数据、图标、颜色、默认角色 |
| `clearance-users-write` | 为特定用户分配/移除角色 |

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

::: tip 授予窄范围的访问权限
将 `clearance-access` 与恰好一个 `clearance-{section}-write` 权限组合，即可把某个面板分区（例如角色）的写权限交给某个用户（比如一位团队负责人），而不会暴露设置、看守器或用户管理。
:::

如果某个精细化权限记录在数据库中完全不存在（一种历史遗留的、未预置的状态），检查会回退到 `clearance-access`，以保持向后兼容。在任何已经运行过 `clearance:install` 的实例上，这个回退逻辑永远不会被触发。

## 受保护的 `clearance-*` 权限

`clearance-*` 权限是被保留的：除了通过该包自身的安装流程之外，任何人都无法创建、重命名、删除它们，也无法将其添加到任何角色——即使是 `super_admin` 本身的操作者，针对 `super_admin` 角色本身也不例外。

## 下一步

- [权限](/zh/features/permissions)——通用的权限 CRUD 面板（不包含 `clearance-*`）。
- [角色](/zh/features/roles)——将 `clearance-*-write` 权限分配给一个自定义角色。
- [安全性](/zh/guide/security)——完整的权限提升与上限模型。
