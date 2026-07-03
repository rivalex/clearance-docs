# 简介

## 什么是 Clearance？

Clearance 是一个基于 [spatie/laravel-permission](https://github.com/spatie/laravel-permission) 构建的、开箱即用的 Laravel 应用管理面板。它提供了一个 Livewire 4 + Flux UI 面板，用于管理权限、角色、看守器和设置，并附带一个上下文授权层和角色上限功能——通过一条 artisan 命令即可安装，且不改动任何 Spatie 数据表。

Clearance 为你提供：

- **管理面板**，涵盖权限、角色、看守器、设置和仪表板——既可以使用预置路由，也可以单独嵌入每个 Livewire 组件。
- **上下文授权**——将角色或权限授予限定在特定模型实例的范围内（一个 Store、一个 Tenant、一个 Project），通过 `$user->canIn()`、`@canin`/`@hasrolein` 这两个 Blade 指令，以及可在 User 模型之外使用的 `ContextService`。
- **上限角色（Ceiling roles）**——一个角色可以声明一个父角色，其权限集合将作为上限；子角色的权限永远不会超出父角色，并在每次保存时自动强制执行。
- **精细化的写权限**——每个面板分区都有一个可单独委派的 `clearance-{section}-write` 权限，因此你可以只授予“管理角色”而不必同时授予“管理设置”。
- **超级管理员**，带有可选启用的全局 Gate 绕过、针对现有类管理员角色的别名检测，以及安全、非破坏性的提升。
- **用户模块**（可选启用）——从针对单个用户的面板中，将角色分配到全局或特定上下文，并支持按上下文设置权限覆盖。
- **设置面板**——针对每个角色/看守器的显示名称、描述、经过净化的 SVG 图标及颜色；一个支持自动分配与批量分配的可配置默认角色。
- **看守器管理**——数据库支持的看守器，在应用启动时注入到 `auth.guards`，且仅限于白名单内的驱动程序。
- **内置 9 种语言**（ar、en、es、fr、hi、it、pt、ru、zh），经过键一致性测试。
- **可扩展钩子**——`HasClearance` trait、`HasPermissionGroups` trait、`ClearanceContextable` contract，以及可复用的 Blade 组件。
- **SVG 净化**——每一个用户提供的图标在存储前都会经过一个白名单净化器处理。
- **经过安全加固**——在每一条授予权限的路径上都设有权限提升上限，拒绝优先的上下文覆盖机制，以及受保护的 `clearance-*` 命名空间。参见[安全性](/zh/guide/security)。

## 与 spatie/laravel-permission 的关系

Clearance 并不是 Spatie 权限包的替代品——它是构建在其之上的 UI 与授权层。角色和权限依然是 Spatie 的 `Role` 和 `Permission` 模型，依然存储在 Spatie 的 `roles`、`permissions`、`model_has_roles`、`model_has_permissions` 和 `role_has_permissions` 数据表中。Clearance 从不修改这些数据表。Clearance 新增的一切内容——显示元数据、角色作用域、上限、上下文分配、按上下文的覆盖——都存放在它自己的一组 `clr_*` 数据表中。完整拆解请参见[架构](/zh/guide/architecture)。

## 环境要求

| 依赖 | 版本 |
|---|---|
| PHP | ^8.3 |
| Laravel | ^11.0 \| ^12.0 \| ^13.0 |
| spatie/laravel-permission | ^6.0 |
| livewire/livewire | ^3.0 \| ^4.0 |
| livewire/flux | ^2.14 |

## 核心概念

| 概念 | 概述 |
|---|---|
| 权限（Permission） | 一个遵循 `group-action` 命名约定的 Spatie 权限（例如 `orders-create`）。授予某项能力。 |
| 角色作用域（Role scope） | 每个角色要么是 `global`（直接分配、全局生效），要么是 `contextual`（绑定到特定模型类型，只能在某个上下文实例内分配）。 |
| 上限角色（Ceiling role） | 一个角色可以声明一个父角色，其权限集合作为上限——子角色永远不会超出这个上限。 |
| 上下文（Context） | 一个模型实例（一个 Store、一个 Tenant、一个 Project），角色或权限覆盖可以限定在其范围内。 |
| 覆盖（Override） | 一个按用户、按上下文的 `forced_on`/`forced_off` 权限授予或拒绝——拒绝始终优先生效。 |

以上每个概念都有对应的完整页面：[权限](/zh/features/permissions)、[角色](/zh/features/roles)、[上下文授权](/zh/features/contextual-authorization)、[用户](/zh/features/users)、[超级管理员](/zh/features/super-admin)。

## 下一步

准备好安装 Clearance 了吗？继续阅读[安装指南](/zh/guide/installation)。
