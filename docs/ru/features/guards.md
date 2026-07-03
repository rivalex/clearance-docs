# Guards

Панель Guards управляет guard'ами аутентификации как записями в базе данных, поэтому guard'ы можно добавлять или изменять без редактирования `config/auth.php` и повторного деплоя.

## Что делает панель

- **Создание / редактирование / удаление** guard'ов, каждый с полями `name`, `driver` и `provider`.

<Screenshot src="/screenshots/guards-page.png" alt="Панель Guards Clearance со списком guard'ов, хранящихся в базе данных" caption="Панель Guards перечисляет каждый guard, хранящийся в базе данных, вместе с его драйвером и провайдером." />

## Как применяются guard'ы

Guard'ы, управляемые через панель, внедряются в `auth.guards` при загрузке приложения, поэтому система аутентификации Laravel распознаёт их ещё до выполнения какого-либо запроса - без ручного редактирования `config/auth.php`.

## Разрешённые драйверы (allow-list)

Принимаются только драйверы из allow-list:

```php
// config/clearance.php
'allowed_guard_drivers' => ['session', 'token', 'jwt', 'passport', 'sanctum'],
```

::: warning Драйверы вне списка пропускаются, а не отклоняются с ошибкой
Guard с драйвером, которого нет в `allowed_guard_drivers`, пропускается при загрузке (с предупреждением в лог), а не приводит к повреждению конфигурации аутентификации. Если созданный вами guard не работает, сначала проверьте его драйвер по allow-list.
:::

## Контроль доступа

Доступ на чтение требует `clearance-access`. Создание, редактирование или удаление guard'а требует `clearance-guards-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-guards-write');
```

## Маршрут и встроенный компонент

| URI | Имя маршрута | Описание |
|---|---|---|
| `/clearance/guards` | `clearance.guards` | Управление guard'ами (создание/редактирование/удаление) |

```blade
<livewire:clearance::guards.guard-manager />
```

## База данных

| Таблица | Ключевые столбцы |
|---|---|
| `clr_guards` | `name` (уникален), `driver`, `provider` |

## Следующие шаги

- [Справочник конфигурации](/ru/guide/configuration) - полные ключи конфигурации `allowed_guard_drivers` и `guards`.
- [Справочник фасада](/ru/api/facade) - `Clearance::guards()` возвращает имена всех управляемых guard'ов аутентификации.
