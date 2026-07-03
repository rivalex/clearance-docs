# Архитектура

## Как работает разрешение (resolution)

`resolveFor()` - это ядро контекстной авторизации Clearance - см. [Контекстную авторизацию](/ru/features/contextual-authorization) для полного API. Он объединяет три источника в следующем порядке приоритета:

1. **Контекстные выдачи через роли** - разрешения от ролей, которыми пользователь обладает именно в этом контексте (`clr_role_ctx`).
2. **Глобальные выдачи через роли** - разрешения от глобально назначенных пользователю ролей Spatie (так `super_admin` и другие глобальные роли удовлетворяют `canIn()` для любого контекста).
3. **Переопределения по контексту** (`clr_ctx_overrides`) - `forced_on` добавляет разрешение поверх вышеперечисленного; `forced_off` убирает его, даже если разрешение было выдано глобальной ролью. Запрет всегда побеждает.

::: warning Запрет всегда побеждает
`forced_off` перекрывает любое разрешение, выданное глобальной ролью или контекстной выдачей через роль. Не существует способа "перебить" переопределение `forced_off` для той же пары пользователь/разрешение/контекст, кроме как удалить его.
:::

## База данных

Clearance владеет 6 таблицами и никогда не изменяет основные таблицы Spatie (`roles`, `permissions`, `model_has_roles`, `model_has_permissions`, `role_has_permissions`). Все внешние ключи, ссылающиеся на `roles.id`/`permissions.id`, каскадно удаляются, если не указано иное; таблицы Spatie не имеют внешних ключей, ссылающихся обратно на `clr_*`.

| Таблица | Ключевые столбцы |
|---|---|
| `clr_meta` | `subject_type`, `subject_key` (уникальны вместе) - `display_name`, `description`, `icon_svg`, `color` - метаданные отображения для роли или guard'а |
| `clr_role_meta` | `role_id` (уникален, FK -> `roles`) - `is_locked`, `scope` (`global`\|`contextual`), `context_types` (json) - `parent_role_id` (nullable FK -> `roles`, `ON DELETE SET NULL`) - ceiling-роль |
| `clr_guards` | `name` (уникален), `driver`, `provider` - guard'ы, управляемые через панель |
| `clr_settings` | `key` (уникален), `value` - хранилище ключ/значение времени выполнения (роль по умолчанию и т.д.) |
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - контекстные назначения ролей, уникальны по `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) - персональные, привязанные к контексту переопределения разрешений |

## Никогда не трогает таблицы Spatie

Clearance спроектирован так, чтобы работать рядом с `spatie/laravel-permission`, а не заменять его. Каждая запись `Role` и `Permission`, создаваемая или редактируемая панелью Clearance, - это настоящая модель Spatie, хранящаяся в собственных таблицах Spatie. Clearance никогда не выполняет миграцию, которая добавляет столбец в `roles`, `permissions`, `model_has_roles`, `model_has_permissions` или `role_has_permissions`, либо записывает в них внешний ключ.

Все собственные данные Clearance - метаданные отображения, область и ceiling ролей, контекстные назначения, переопределения по контексту, определения guard'ов и настройки - хранятся исключительно в 6 таблицах `clr_*` выше. Это означает:

- Удаление Clearance никогда не рискует повредить существующие данные ролей/разрешений Spatie.
- Любой код, который уже напрямую читает модели или таблицы Spatie, продолжает работать без изменений.
- Контекстные и ceiling-функции Clearance - это чисто аддитивные слои, разрешаемые во время запроса (через `resolveFor()` и `RoleService`), а не встроенные в схему Spatie.

## Смотрите также

- [Контекстная авторизация](/ru/features/contextual-authorization) - `canIn()`, `hasRoleIn()`, директивы Blade, `ContextService`
- [Роли](/ru/features/roles) - область и ceiling-роли, `RoleService::setParent()`
- [Миграция](/ru/guide/migration) - заполнение `clr_meta`/`clr_role_meta`/`clr_guards` на существующей установке Spatie
- [Безопасность](/ru/guide/security) - как модель ceiling и переопределений предотвращает повышение привилегий
