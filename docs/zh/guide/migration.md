# 从已有的 Spatie 安装迁移

如果你的应用已经在使用 `spatie/laravel-permission`，并且已经存在角色、权限和分配关系，`clearance:install` 会检测并保留它们——只要 `roles` 表已经存在，它就绝不会运行 Spatie 的迁移。完整的安装流程请参见[安装](/zh/guide/installation)。

安装完成后，使用 `clearance:backfill` 将 Clearance 自身的元数据补充到你现有的角色和看守器上。

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

## 各部分的作用

| 部分 | 效果 |
|---|---|
| `meta` | 为每个尚无显示名称的角色，生成一个（标题格式化的角色名）作为 `clr_meta` 显示名称。 |
| `roles` | 为每个尚无对应记录的角色，生成 `clr_role_meta` 默认值（`scope=global`、`is_locked=false`）。 |
| `guards` | 从 `config('auth.guards')` 导入看守器到 `clr_guards`，并按 `allowed_guard_drivers` 过滤。 |

使用 `--only=<section>` 只运行单个部分，而不是全部三个。

::: tip 幂等
所有部分都是幂等的——重复运行 `clearance:backfill` 是安全的，不会重复写入或覆盖已有的 `clr_*` 记录。
:::

## 边缘情况

该包的代码仓库中有一份更深入的迁移指南，涵盖了本页未总结的边缘情况——特别是当你的数据库中已经存在一个名为 `super_admin`（或别名如 `superadmin`、`root`、`owner`）的角色，与 Clearance 自身的 `super_admin` 角色发生冲突时会发生什么，以及关于自定义 `Role`/`Permission` 模型子类和看守器导入行为的说明。在对一个已有大量角色数据的生产数据库运行 backfill 之前，请务必先阅读该指南。另请参见[超级管理员](/zh/features/super-admin)，了解 `clearance:install` 期间的别名检测行为。

## 相关链接

- [安装](/zh/guide/installation)——完整的 `clearance:install` 流程
- [Artisan 命令](/zh/api/artisan-commands)——完整的命令参考
- [架构](/zh/guide/architecture)——backfill 所填充的 `clr_*` 数据表
