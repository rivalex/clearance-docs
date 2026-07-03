# 安全性

Clearance 在发布前经历了一次专门的安全审计，以及此后的两轮加固。要点包括：在每一条授予权限的路径上（无论是直接授权还是角色级同步）都设有权限提升上限、拒绝优先的上下文覆盖机制、一个采用白名单策略的 SVG 净化器、一个受保护的 `clearance-*` 权限命名空间，以及对访客安全的 Blade 指令。

## 面板访问与精细化的写权限

对整个面板的读访问需要 `clearance-access` 权限（通过 `can()` 检查，绝不通过 `hasRole()`）。每个分区的写操作都由一个专属的、可委派的权限控制——全部由 `clearance:install` 创建并分配给 `super_admin`：

| 权限 | 控制内容 |
|---|---|
| `clearance-permissions-write` | 创建/编辑/删除权限 |
| `clearance-roles-write` | 创建/编辑/删除角色及其权限分配 |
| `clearance-guards-write` | 创建/编辑/删除看守器 |
| `clearance-settings-write` | 编辑显示元数据、图标、颜色、默认角色 |
| `clearance-users-write` | 为特定用户分配/移除角色 |

如果某个精细化权限记录在数据库中完全不存在（一种历史遗留的、未预置的状态），检查会回退到 `clearance-access`，以保持向后兼容。

## 受保护的 `clearance-*` 命名空间

`clearance-*` 权限是被保留的：除了通过该包自身的安装流程之外，任何人都无法创建、重命名、删除它们，也无法将其添加到任何角色——即使是 `super_admin` 本身的操作者，针对 `super_admin` 角色本身也不例外。

## 权限提升上限

Clearance 中每一条授予权限的路径都强制执行一个上限，使得操作者永远无法授予超出自己所持有的权限：

- **上限角色**——一个角色可以声明一个父角色，其权限集合作为上限。任何超出的权限都会在 `RoleService::setParent()` 以及此后每一次权限同步时被默默裁剪——没有异常，也没有错误提示。从父角色移除权限会自动级联到每一个子角色。完整的上限 API 请参见[角色](/zh/features/roles)。
- **用户模块**——角色分配以及直接/覆盖式的权限授予都强制执行一个权限提升上限：非 `super_admin` 的操作者无法修改自己的分配，也无法授予自己都不持有的权限。参见[用户](/zh/features/users)。

## 拒绝优先的上下文覆盖

按上下文的权限覆盖（`clr_ctx_overrides`）支持 `forced_on` 和 `forced_off`。`forced_off` 始终优先生效——即使是由全局角色授予的权限也不例外。没有任何路径能让一个优先级更低的授权覆盖一个明确的拒绝。完整的解析顺序请参见[架构](/zh/guide/architecture#解析机制)。

## SVG 净化

每一个用户提供的图标（设置面板中的角色/看守器显示元数据）在存储前以及每次渲染时都会经过一个白名单净化器处理——而不仅仅是上传时的一次性检查。

## 对访客安全的 Blade 指令

`@canin` 和 `@hasrolein` 对访客用户都会安全地解析（返回 `false`）——它们从不抛出异常。

## Gate 绕过是应用范围内的

`super_admin_gate_bypass`（默认 `false`）控制 `super_admin` 角色是否通过一个 `Gate::before()` 钩子，在全局范围内绕过所有 Laravel 的 `can()`/`Gate::allows()`/策略检查。

::: warning 并非仅限于面板范围
启用后，任何持有 `super_admin` 的用户都会跳过整个应用范围内的所有 Gate 检查——不仅仅是 Clearance 面板的检查。请谨慎地有意启用它。别名检测与提升行为请参见[超级管理员](/zh/features/super-admin)。
:::

## 与安全相关的注意事项

- `forced_off` 始终优先生效——即使是由全局角色授予的权限也不例外。
- 上限角色只会裁剪权限，从不授予权限——设置一个权限宽松的父角色本身不会给子角色带来任何权限。
- Gate 绕过（`super_admin_gate_bypass`）是应用范围内的，而非仅限于面板范围。
- 用户模块默认关闭（`modules.users = false`）。
- `clearance-*` 权限在安装流程之外是不可触碰的，即使对 `super_admin` 也是如此。
- 在全新环境中，请始终运行 `clearance:install`，而不是直接运行 `migrate`——参见[安装](/zh/guide/installation)。

## 相关链接

- [架构](/zh/guide/architecture)——解析优先级与 `clr_*` 表结构
- [角色](/zh/features/roles)——深入了解上限角色
- [用户](/zh/features/users)——角色/权限分配上的权限提升上限
- [超级管理员](/zh/features/super-admin)——Gate 绕过与别名检测
