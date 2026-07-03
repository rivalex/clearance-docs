# Конфигурация

Clearance поставляется с разумными значениями по умолчанию, поэтому он работает "из коробки" после [установки](/ru/guide/installation). Опубликуйте файл конфигурации, когда вам нужно настроить префикс маршрута, middleware, контекстные модели или любую другую настройку.

```bash
php artisan vendor:publish --tag=clearance-config
```

Это публикует `config/clearance.php`.

## Справочник конфигурации

Это исчерпывающий справочник по каждому ключу в `config/clearance.php`. Другие страницы этой документации ссылаются сюда вместо повторения таблицы.

| Ключ | По умолчанию | Назначение |
|---|---|---|
| `route_prefix` | `'clearance'` | Префикс URI для всех маршрутов панели. |
| `middleware` | `['web', 'auth']` | Применяется ко всем маршрутам панели перед `clearance.access`. |
| `access_permission` | `'clearance-access'` | Разрешение, проверяемое middleware доступа. |
| `user_model` | `null` | По умолчанию `auth.providers.users.model`. |
| `modules.users` | `false` | Главный переключатель модуля пользователей (маршруты + компоненты Livewire). |
| `contextual_models` | `[]` | `FQCN => ['label', 'icon', 'label_attribute', 'searchable']` - модели, которые могут выступать контекстами для привязки ролей. |
| `enforce_naming_convention` | `true` | Принудительное соблюдение именования разрешений `group-action`. |
| `naming_separator` | `'-'` | Символ-разделитель - только `'-'` или `'_'`. |
| `guards` | `[]` | Переопределить автоматически определённые guard'ы из `config/auth.php`. Пусто = автоопределение всех. |
| `allowed_guard_drivers` | `['session', 'token', 'jwt', 'passport', 'sanctum']` | Драйверы, принимаемые при внедрении guard'ов из БД в `auth.guards`. |
| `layout` | `null` | Blade-layout для полностраничных компонентов. `null` = layout по умолчанию основного приложения (`config('livewire.layout')`). |
| `auto_assign_default_role` | `false` | Автоматически назначать настроенную в Настройках роль по умолчанию при событии `Registered`. |
| `super_admin_gate_bypass` | `false` | Опционально: `super_admin` обходит все проверки Gate во всём приложении. |
| `ui.flux_pro` | `null` | `null` = автоопределение через `Flux::pro()`. |

## Соглашение об именовании разрешений

Разрешения должны следовать формату `group-action`:

```
orders-create       correct
orders-read         correct
store-orders-delete correct

create              wrong (bare action, no group)
orders.create       wrong (dot separator)
Orders-Create       wrong (camelCase)
```

Отключите принудительное соблюдение для конкретного окружения с помощью `enforce_naming_convention`:

```php
'enforce_naming_convention' => false,
```

::: warning Разделитель проверяется при загрузке
Значение `naming_separator` - единственная часть этой конфигурации, которая попадает в сырой фрагмент SQL (группировка/сортировка разрешений), поэтому допускаются только `-` или `_`.
:::

## Настройка middleware

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

Имена маршрутов остаются стабильными независимо от изменений префикса или middleware - см. [Быстрый старт](/ru/guide/quick-start) для полной таблицы маршрутов.

## Смотрите также

- [Быстрый старт](/ru/guide/quick-start) - подключение панели с применёнными этими настройками
- [Публикация ресурсов](/ru/guide/publishing) - публикация переводов и объяснение ключа `layout`
- [Guards](/ru/features/guards) - как используются `guards` и `allowed_guard_drivers`
- [Super Admin](/ru/features/super-admin) - как работает `super_admin_gate_bypass`
