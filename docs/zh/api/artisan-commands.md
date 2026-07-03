# Artisan 命令

Clearance 提供两个 Artisan 命令：`clearance:install` 用于首次安装，`clearance:backfill` 用于为已有安装填充默认值。完整的端到端安装流程请参见[安装指南](/zh/guide/installation.md)。

## `clearance:install`

```bash
php artisan clearance:install
php artisan clearance:install --user=1
php artisan clearance:install --role=editor
php artisan clearance:install --super-admin-role=owner
php artisan clearance:install --force
```

| 参数 | 说明 |
|---|---|
| `--user=ID` | 安装完成后为某个用户分配 `super_admin` 角色。 |
| `--role=NAME` | 将 `clearance-access` 分配给某个角色（若不存在则创建）。 |
| `--super-admin-role=NAME` | 将现有角色提升为 `super_admin`（跳过交互式提示）。 |
| `--force` | 即使已经安装过，也强制重新运行。 |

### 执行顺序

1. 发布 `config/clearance.php`。
2. 发布 Clearance 的迁移文件。
3. 检测 Spatie Permission 的数据表——如果 `roles` 表不存在，则先自动发布并运行 Spatie 的迁移。
4. 运行所有待执行的迁移。
5. 创建 `clearance-access` 以及全部 5 个精细化能力权限：`clearance-permissions-write`、`clearance-roles-write`、`clearance-guards-write`、`clearance-settings-write`、`clearance-users-write`。
6. 创建 `super_admin` 角色，并分配所有 `clearance-*` 权限。此步骤是增量式操作——重复运行不会移除已有权限。
7. 写入 `storage/.clearance-installed` 作为幂等性标记。

### 别名检测

如果数据库中已经存在一个名为 `super_admin`、`superadmin`、`super-admin`、`root` 或 `owner` 的角色，安装程序会检测到它，并交互式地提示将其提升，或创建一个单独的 `super_admin` 角色。使用 `--super-admin-role=NAME` 可以非交互式地跳过该提示。

提升会原地重命名该角色——所有 `model_has_roles` 分配都会通过 `role_id` 上的外键被保留。

::: warning
`clearance-*` 权限在安装流程之外受到保护——参见[`PermissionService`](/zh/api/services.md#permissionservice)以及[服务](/zh/api/services.md#异常)中的 `ClearanceProtectedResourceException`。
:::

## `clearance:backfill`

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

| 参数 | 说明 |
|---|---|
| `--only=meta\|roles\|guards` | 只运行某一个 backfill 部分。 |
| `--dry-run` | 预览变更但不写入。 |

### 各部分

| 部分 | 说明 |
|---|---|
| `meta` | 为每个尚无显示名称的角色，生成一个（标题格式化的角色名）作为 `clr_meta` 显示名称。 |
| `roles` | 为每个尚无对应记录的角色，生成 `clr_role_meta` 默认值（`scope=global`、`is_locked=false`）。 |
| `guards` | 从 `config('auth.guards')` 导入看守器到 `clr_guards`，并按 `allowed_guard_drivers` 过滤。 |

所有部分都是幂等的——重复运行 `clearance:backfill` 是安全的，只会填补缺失的部分。

::: tip
对于已有的 Spatie 安装：只要 `roles` 表已经存在，`clearance:install` 就绝不会运行 Spatie 的迁移，因此现有的角色、权限和分配关系会被自动保留。
:::

## 另请参阅

- [服务](/zh/api/services.md)——支撑 `guards` 和 `meta` backfill 部分的 `GuardService` 和 `MetaService`。
- [迁移指南](/zh/guide/migration.md)——将一个已有的 Spatie Permission 安装迁移到 Clearance。
