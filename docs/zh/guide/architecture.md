# 架构

## 解析机制

`resolveFor()` 是 Clearance 上下文授权的核心——完整 API 请参见[上下文授权](/zh/features/contextual-authorization)。它按以下优先级顺序合并三个来源：

1. **上下文角色授权**——来自用户在此上下文中专门持有的角色所授予的权限（`clr_role_ctx`）。
2. **全局角色授权**——来自用户全局分配的 Spatie 角色所授予的权限（因此 `super_admin` 及其他全局角色对任意上下文的 `canIn()` 检查都能满足）。
3. **按上下文的覆盖**（`clr_ctx_overrides`）——`forced_on` 在上述基础上追加一个权限；`forced_off` 则移除它，即使该权限是由全局角色授予的也是如此。拒绝始终优先生效。

::: warning 拒绝始终优先生效
`forced_off` 会覆盖任何由全局角色或上下文角色授权所授予的权限。对于同一个用户/权限/上下文组合，除了移除该 `forced_off` 覆盖之外，没有任何方法能“压过”它。
:::

## 数据库

Clearance 拥有 6 个数据表，从不修改 Spatie 的核心数据表（`roles`、`permissions`、`model_has_roles`、`model_has_permissions`、`role_has_permissions`）。所有引用 `roles.id`/`permissions.id` 的外键都会在删除时级联，除非另有说明；Spatie 的数据表没有任何外键指向 `clr_*`。

| 数据表 | 关键列 |
|---|---|
| `clr_meta` | `subject_type`、`subject_key`（联合唯一）——`display_name`、`description`、`icon_svg`、`color`——角色或看守器的显示元数据 |
| `clr_role_meta` | `role_id`（唯一，外键 -> `roles`）——`is_locked`、`scope`（`global`\|`contextual`）、`context_types`（json）——`parent_role_id`（可空外键 -> `roles`，`ON DELETE SET NULL`）——上限角色 |
| `clr_guards` | `name`（唯一）、`driver`、`provider`——通过面板管理的看守器 |
| `clr_settings` | `key`（唯一）、`value`——运行时键/值存储（默认角色等） |
| `clr_role_ctx` | `user_id`、`role_id`（外键 -> `roles`）、`context_type`、`context_id`——上下文角色分配，按 `(user, role, context_type, context_id)` 联合唯一 |
| `clr_ctx_overrides` | `user_id`、`role_id`（外键 -> `roles`）、`permission_id`（外键 -> `permissions`）、`context_type`、`context_id`、`type`（`forced_on`\|`forced_off`）——按用户、按上下文的权限覆盖 |

## 从不触碰 Spatie 的数据表

Clearance 的设计是与 `spatie/laravel-permission` 并存，而不是取代它。Clearance 面板创建或编辑的每一条 `Role` 和 `Permission` 记录，都是货真价实的 Spatie 模型，存储在 Spatie 自己的数据表中。Clearance 从不运行任何会向 `roles`、`permissions`、`model_has_roles`、`model_has_permissions` 或 `role_has_permissions` 中任意一张表添加列、或写入外键的迁移。

Clearance 自身的所有数据——显示元数据、角色作用域与上限、上下文分配、按上下文的覆盖、看守器定义以及设置——完全存放在上述 6 张 `clr_*` 数据表中。这意味着：

- 卸载 Clearance 永远不会有损坏你现有 Spatie 角色/权限数据的风险。
- 任何已经直接读取 Spatie 模型或数据表的代码都能不加修改地继续运行。
- Clearance 的上下文和上限功能都是在查询时解析（通过 `resolveFor()` 和 `RoleService`）的纯附加层，并没有嵌入 Spatie 的表结构中。

## 相关链接

- [上下文授权](/zh/features/contextual-authorization)——`canIn()`、`hasRoleIn()`、Blade 指令、`ContextService`
- [角色](/zh/features/roles)——作用域与上限角色、`RoleService::setParent()`
- [迁移](/zh/guide/migration)——将 `clr_meta`/`clr_role_meta`/`clr_guards` 补充到已有的 Spatie 安装中
- [安全性](/zh/guide/security)——上限与覆盖模型如何防止权限提升
