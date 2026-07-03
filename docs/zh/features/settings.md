# 设置

设置面板配置角色和看守器在 Clearance 面板其余部分中的展示方式，以及新用户所分配的默认角色。

## 该面板的功能

- **显示元数据**——针对每个角色/看守器的显示名称、描述，以及一个经过净化的 SVG 图标 + 十六进制颜色，会展示在整个面板中。
- **默认角色**——当 `auto_assign_default_role` 为 `true` 时，自动分配给新注册用户的角色。
- **批量分配默认角色**——一键将当前默认角色分配给所有尚未拥有它的现有用户。
- **显示图标**——切换整个面板中的图标显示。

<Screenshot src="/screenshots/settings-page.png" alt="Clearance 设置面板，带默认角色和显示图标开关" caption="设置面板控制默认角色、批量分配以及图标显示。" />

## 每个角色/看守器的显示元数据

<Screenshot src="/screenshots/settings-role-meta.png" alt="带图标和颜色选择器的角色显示元数据表单" caption="编辑某个角色的显示名称、描述、图标和颜色。" />

每一个用户提供的 SVG 图标在存储前以及每次渲染时都会经过一个白名单净化器处理，因此可以放心编辑显示元数据，而不必担心向面板引入不安全的标记内容。

## 默认角色与自动分配

```php
// config/clearance.php
'auto_assign_default_role' => true, // requires a default role set in Settings
```

::: tip 在启用自动分配之前先设置默认角色
在设置面板中配置默认角色之前，`auto_assign_default_role` 不会产生任何效果。请先设置默认角色，再启用该开关。
:::

随时可以在面板中使用**批量分配**功能，将当前默认角色回溯分配给尚未拥有它的现有用户——这在更改默认角色之后，或在为已有用户的应用启用 `auto_assign_default_role` 之后特别有用。

## 访问控制

读访问需要 `clearance-access`。编辑显示元数据、图标、颜色或默认角色需要 `clearance-settings-write`：

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-settings-write');
```

## 路由与嵌入式组件

| URI | 路由名称 | 说明 |
|---|---|---|
| `/clearance/settings` | `clearance.settings` | 显示元数据（图标、颜色）、默认角色、常规设置 |

```blade
<livewire:clearance::settings.settings-manager />
```

## 数据库

| 数据表 | 关键列 |
|---|---|
| `clr_meta` | `subject_type`、`subject_key`（联合唯一）——`display_name`、`description`、`icon_svg`、`color` |
| `clr_settings` | `key`（唯一）、`value` |

## 下一步

- [角色](/zh/features/roles)——在此处配置显示元数据的角色。
- [看守器](/zh/features/guards)——在此处配置显示元数据的看守器。
- [安全性](/zh/guide/security)——SVG 净化器的工作原理。
