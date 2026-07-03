# Разрешения

Панель Разрешений - это полноценный CRUD-интерфейс над моделью `Permission` из Spatie. Clearance никогда не вводит собственную таблицу разрешений - каждое разрешение, созданное здесь, является обычным разрешением Spatie, применимым везде, где используется API Spatie (`$user->can()`, `Gate::allows()`, политики, `@can`).

## Что делает панель

- **Создание** - добавление нового разрешения, проверенного по [соглашению об именовании](#permission-naming-convention) (если оно не отключено) и привязанного к guard'у.
- **Редактирование** - переименование разрешения или изменение его guard'а.
- **Удаление** - удаление разрешения и его назначений ролям/пользователям (стандартное каскадное поведение Spatie).

<Screenshot src="/screenshots/permissions-page.png" alt="Панель Разрешений Clearance со списком разрешений, сгруппированных по префиксу именования" caption="Панель Разрешений перечисляет каждое разрешение Spatie, сгруппированное по префиксу именования." />

<Screenshot src="/screenshots/permission-form-modal.png" alt="Форма создания/редактирования разрешения с валидацией соглашения об именовании" caption="Форма разрешения проверяет соглашение об именовании group-action перед сохранением." />

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

Префикс группы (`orders` в `orders-create`) - это то, что [Дашборд](/ru/features/dashboard) использует для подсчёта различных групп разрешений.

::: tip Отключение принудительного соблюдения
Некоторые команды мигрируют с другой схемы именования. Отключите проверку для конкретного окружения:

```php
// config/clearance.php
'enforce_naming_convention' => false,
```
:::

## Контроль доступа

Доступ на чтение к панели (просмотр списка разрешений) требует `clearance-access`. Создание, редактирование или удаление разрешения дополнительно требует `clearance-permissions-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-permissions-write');
```

::: warning Разрешения `clearance-*` зарезервированы
Сами разрешения `clearance-*` (`clearance-access`, `clearance-permissions-write` и т.д.) нельзя создавать, переименовывать или удалять через эту панель - даже актором `super_admin`. Они управляются исключительно собственным процессом установки пакета. См. [Super Admin](/ru/features/super-admin#reserved-clearance-permissions).
:::

## Маршрут и встроенный компонент

| URI | Имя маршрута | Описание |
|---|---|---|
| `/clearance/permissions` | `clearance.permissions` | CRUD разрешений с валидацией именования по префиксу группы |

```blade
<livewire:clearance::permissions.permission-manager />
```

## Следующие шаги

- [Роли](/ru/features/roles) - назначение разрешений роли в рамках guard'а.
- [Super Admin](/ru/features/super-admin) - полная таблица точечных прав на запись.
- [Справочник конфигурации](/ru/guide/configuration) - `enforce_naming_convention`, `naming_separator`.
