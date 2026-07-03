# 快速开始

[安装](/zh/guide/installation)完成后，有两种方式可以展示 Clearance 面板：使用预置路由，或者在应用中的任意位置嵌入单个 Livewire 组件。

## 方式 A——预置路由

安装完成后，面板即可通过 `/clearance`（或已配置的 `route_prefix`，参见[配置](/zh/guide/configuration)）访问。所有路由都受 `clearance.access` 中间件保护，并自动使用宿主应用的 Livewire 布局。

| URI | 路由名称 | 说明 |
|---|---|---|
| `/clearance` | `clearance.home` | 仪表板 |
| `/clearance/guards` | `clearance.guards` | 看守器管理（创建/编辑/删除） |
| `/clearance/permissions` | `clearance.permissions` | 权限 CRUD，带分组前缀命名校验 |
| `/clearance/roles` | `clearance.roles` | 角色 CRUD，带看守器限定的权限分配、作用域（全局/上下文）和上限 |
| `/clearance/settings` | `clearance.settings` | 显示元数据（图标、颜色）、默认角色、常规设置 |
| `/clearance/user/{userId}` | `clearance.user` | 按用户分配角色 + 上下文权限覆盖（需要 `modules.users = true`） |

无论前缀如何变化，路由名称始终保持稳定。自定义中间件：

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

## 方式 B——嵌入式 Livewire 组件

每个面板分区都可以单独挂载到任意 Blade 视图、Filament 页面或自定义路由中：

```blade
<livewire:clearance::dashboard />
<livewire:clearance::permissions.permission-manager />
<livewire:clearance::roles.role-manager />
<livewire:clearance::guards.guard-manager />
<livewire:clearance::settings.settings-manager />
{{-- 用户模块（配置中需要 modules.users = true） --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

## 接下来去哪里

- [配置参考](/zh/guide/configuration)——每一个配置键、默认值及其用途
- [上下文授权](/zh/features/contextual-authorization)——将角色和权限限定在一个 Store、Tenant 或 Project 的范围内
- [角色](/zh/features/roles)——作用域与上限角色
- [仪表板](/zh/features/dashboard)——仪表板组件展示的内容
- [用户](/zh/features/users)——可选启用的按用户面板
