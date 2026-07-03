# Команды Artisan

Clearance поставляется с двумя командами Artisan: `clearance:install` для первоначальной настройки и `clearance:backfill` для заполнения значений по умолчанию на существующих установках. См. [руководство по установке](/ru/guide/installation.md) для полного пошагового описания настройки.

## `clearance:install`

```bash
php artisan clearance:install
php artisan clearance:install --user=1
php artisan clearance:install --role=editor
php artisan clearance:install --super-admin-role=owner
php artisan clearance:install --force
```

| Флаг | Описание |
|---|---|
| `--user=ID` | Назначить роль `super_admin` пользователю после установки. |
| `--role=NAME` | Назначить `clearance-access` роли (создаётся, если отсутствует). |
| `--super-admin-role=NAME` | Повысить существующую роль до `super_admin` (пропускает интерактивный запрос). |
| `--force` | Повторный запуск, даже если уже установлено. |

### Что делает команда, по порядку

1. Публикует `config/clearance.php`.
2. Публикует миграции Clearance.
3. Определяет наличие таблиц Spatie Permission - если таблица `roles` отсутствует, сначала автоматически публикует и запускает миграции Spatie.
4. Выполняет все ожидающие миграции.
5. Создаёт `clearance-access` и все 5 точечных разрешений: `clearance-permissions-write`, `clearance-roles-write`, `clearance-guards-write`, `clearance-settings-write`, `clearance-users-write`.
6. Создаёт роль `super_admin` со всеми назначенными разрешениями `clearance-*`. Этот шаг аддитивен - он никогда не удаляет существующие разрешения при повторном запуске.
7. Записывает `storage/.clearance-installed` как маркер идемпотентности.

### Обнаружение алиасов

Если в базе данных уже есть роль с именем `super_admin`, `superadmin`, `super-admin`, `root` или `owner`, установщик обнаруживает её и интерактивно предлагает либо повысить её, либо создать отдельную роль `super_admin`. Используйте `--super-admin-role=NAME`, чтобы пропустить запрос в неинтерактивном режиме.

Повышение переименовывает роль на месте - все назначения `model_has_roles` сохраняются благодаря внешнему ключу на `role_id`.

::: warning
Разрешения `clearance-*` защищены вне процесса установки - см. [`PermissionService`](/ru/api/services.md#permissionservice) и `ClearanceProtectedResourceException` в [Сервисах](/ru/api/services.md#exceptions).
:::

## `clearance:backfill`

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

| Флаг | Описание |
|---|---|
| `--only=meta\|roles\|guards` | Выполнить только один раздел backfill. |
| `--dry-run` | Предпросмотр изменений без записи. |

### Разделы

| Раздел | Описание |
|---|---|
| `meta` | Заполняет отображаемое имя `clr_meta` (роль в стиле Title Case) для каждой роли, у которой его ещё нет. |
| `roles` | Заполняет значения по умолчанию `clr_role_meta` (`scope=global`, `is_locked=false`) для каждой роли, у которой их ещё нет. |
| `guards` | Импортирует guard'ы из `config('auth.guards')` в `clr_guards`, отфильтрованные по `allowed_guard_drivers`. |

Все разделы идемпотентны - повторный запуск `clearance:backfill` безопасен и лишь заполняет то, чего не хватает.

::: tip
Для уже существующих установок Spatie: `clearance:install` никогда не запускает миграции Spatie, если таблица `roles` уже существует, поэтому существующие роли, разрешения и назначения сохраняются автоматически.
:::

## Смотрите также

- [Сервисы](/ru/api/services.md) - `GuardService` и `MetaService`, которые обеспечивают разделы backfill `guards` и `meta`.
- [Руководство по миграции](/ru/guide/migration.md) - миграция существующей установки Spatie Permission на Clearance.
