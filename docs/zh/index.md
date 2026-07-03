---
layout: home

hero:
  name: "CLEARANCE"
  text: "面向 Laravel 的权限、角色与上下文授权"
  tagline: 一个基于 spatie/laravel-permission 构建、开箱即用的 Livewire 4 + Flux UI 管理面板。上下文授权、上限角色（ceiling roles）、精细化的权限委派——通过一条 artisan 命令即可安装，且不对 Spatie 的数据表做任何改动。
  image:
    light: /clearance-mark-horizontal.svg
    dark: /clearance-mark-horizontal.svg
    alt: Clearance
  actions:
    - theme: brand
      text: 快速开始
      link: /zh/guide/introduction
    - theme: alt
      text: 在 GitHub 上查看
      link: https://github.com/rivalex/clearance

features:
  - icon: 🖥️
    title: 完整的管理面板
    details: 权限、角色、看守器、设置和仪表板——既可以使用预置路由，也可以在应用中的任意位置单独嵌入每个 Livewire 组件。

  - icon: 🎯
    title: 上下文授权
    details: "将角色或权限授予限定在特定模型实例的范围内——一个 Store、一个 Tenant、一个 Project——通过 $user->canIn()、@canin/@hasrolein 这两个 Blade 指令，以及 ContextService。"

  - icon: 🏔️
    title: 上限角色（Ceiling roles）
    details: 一个角色可以声明一个父角色，其权限集合将作为上限。子角色的权限永远不会超出父角色，并在每次保存时自动强制执行。

  - icon: 🔑
    title: 精细化的写权限
    details: 每个面板分区都有一个可单独委派的 clearance-{section}-write 权限，因此你可以只授予“管理角色”而不必同时授予“管理设置”。

  - icon: 👑
    title: 超级管理员，可选启用
    details: 可选启用的全局 Gate 绕过、针对现有类管理员角色的别名检测，以及对现有角色的安全、非破坏性提升。

  - icon: 🗄️
    title: 从不触碰 Spatie 的数据表
    details: Clearance 拥有 6 个专属的 clr_* 数据表，从不修改 roles、permissions、model_has_roles、model_has_permissions 或 role_has_permissions。

  - icon: 🛡️
    title: 经过安全加固
    details: 在每一条授予权限的路径上都设有权限提升上限，拒绝优先的上下文覆盖机制，一个采用白名单策略的 SVG 净化器，以及一个受保护的 clearance-* 命名空间。

  - icon: 👥
    title: 用户模块（可选启用）
    details: 从针对单个用户的面板中，将角色分配到全局或特定上下文，并支持按上下文设置权限覆盖。

  - icon: 🌐
    title: 内置 9 种语言
    details: "ar、en、es、fr、hi、it、pt、ru、zh——每次变更都经过键一致性（key-parity）测试。"
---
