# 仪表板

仪表板是 Clearance 面板的落地页。它一目了然地展示了你应用的授权状态概览——无需任何配置。

## 展示内容

- **角色总数**——所有 Spatie 角色的数量。
- **看守器总数**——由 Clearance 管理的所有看守器的数量（参见[看守器](/zh/features/guards)）。
- **权限总数**——所有 Spatie 权限的数量。
- **权限组**——不同权限组的数量，源自命名前缀（`group-action` 中的 `group`，例如 `orders-create` 中的 `orders`）。命名约定请参见[权限](/zh/features/permissions)。
- **上下文角色分配**——限定在特定模型实例（一个 Store、一个 Tenant、一个 Project）范围内的角色分配数量。参见[上下文授权](/zh/features/contextual-authorization)。
- **按用户数排名前 5 的角色**——分配用户数最多的五个角色，按排名列出。

<Screenshot src="/screenshots/dashboard-page.png" alt="Clearance 仪表板显示角色、看守器和权限数量" caption="仪表板汇总了角色、看守器、权限、权限组、上下文分配以及排名靠前的角色。" />

## 访问仪表板

| URI | 路由名称 | 说明 |
|---|---|---|
| `/clearance` | `clearance.home` | 仪表板 |

阅读仪表板需要 `clearance-access` 权限，与其他任何面板分区相同。完整的访问控制表请参见[超级管理员](/zh/features/super-admin#面板访问与精细化的写权限)。

## 嵌入组件

仪表板是一个自包含的 Livewire 组件。可以将其挂载到你自己的布局中的任意位置——无论是替代还是补充预置的 `/clearance` 路由：

```blade
<livewire:clearance::dashboard />
```

## 下一步

- [权限](/zh/features/permissions)——管理为仪表板计数提供数据的权限列表。
- [角色](/zh/features/roles)——管理“按用户数排名前 5”这一统计数据背后的角色。
- [上下文授权](/zh/features/contextual-authorization)——了解什么是上下文角色分配。
