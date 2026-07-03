# Blade 组件

Clearance 在 `clearance::` 命名空间下注册了一小组 Blade 组件。它们都可以直接在你自己应用的视图中复用，而不仅限于面板内部。

## 组件

### `x-clearance::message`

消息闪存/状态展示。

```blade
<x-clearance::message>
    Changes saved successfully.
</x-clearance::message>
```

### `x-clearance::clipboard`

复制到剪贴板的按钮/包装组件——例如用于展示路由名称或 ID，并提供一键复制操作。

```blade
<x-clearance::clipboard :value="$route->name" />
```

### `x-clearance::validation-errors`

标准的 Laravel 验证错误列表渲染器。

```blade
<x-clearance::validation-errors />
```

### `x-clearance::icon.*`

一小套内置图标集，用于整个面板。除了 Livewire 本身就依赖的 Alpine 之外，没有其他外部 JS 依赖。

```blade
<x-clearance::icon.check />
```

## Livewire 组件

作为参考，以下是该包注册的完整全页/可嵌入 Livewire 组件名称，可在你自己的视图中通过 `<livewire:clearance::...>` 嵌入使用：

| 组件名称 | 说明 |
|---|---|
| `dashboard` | 面板仪表板。 |
| `permissions.permission-manager` | 权限 CRUD 面板。 |
| `roles.role-manager` | 角色 CRUD 面板，包含上限角色。 |
| `guards.guard-manager` | 看守器 CRUD 面板。 |
| `settings.settings-manager` | 设置面板（显示元数据、图标、颜色）。 |
| `users.user-clearance-manager` | 按用户的角色/权限分配面板。接受 `:userId`。 |

```blade
<livewire:clearance::dashboard />

<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

每个面板对应的功能页面请参见：[仪表板](/zh/features/dashboard.md)、[权限](/zh/features/permissions.md)、[角色](/zh/features/roles.md)、[看守器](/zh/features/guards.md)、[设置](/zh/features/settings.md)、[用户](/zh/features/users.md)。

::: tip
`config('clearance.ui.flux_pro')` 键控制这些组件中是否渲染仅 Pro 版才有的 Flux UI 元素。`null`（默认值）会通过 `Flux::pro()` 自动检测。
:::
