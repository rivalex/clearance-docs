# Компоненты Blade

Clearance регистрирует небольшой набор компонентов Blade в пространстве имён `clearance::`. Все они многократно используются напрямую в представлениях вашего собственного приложения, а не только внутри панели.

## Компоненты

### `x-clearance::message`

Отображение флеш-сообщения/статуса.

```blade
<x-clearance::message>
    Changes saved successfully.
</x-clearance::message>
```

### `x-clearance::clipboard`

Кнопка/обёртка "скопировать в буфер обмена" - используется, например, для отображения имён маршрутов или ID с возможностью копирования в один клик.

```blade
<x-clearance::clipboard :value="$route->name" />
```

### `x-clearance::validation-errors`

Стандартный рендерер списка ошибок валидации Laravel.

```blade
<x-clearance::validation-errors />
```

### `x-clearance::icon.*`

Небольшой встроенный набор иконок, используемый во всей панели. Нет внешних JS-зависимостей, кроме Alpine, который уже требует Livewire.

```blade
<x-clearance::icon.check />
```

## Livewire-компоненты

Для справки: это полностраничные/встраиваемые имена Livewire-компонентов, зарегистрированные пакетом, доступные для встраивания через `<livewire:clearance::...>` в ваших собственных представлениях:

| Имя компонента | Описание |
|---|---|
| `dashboard` | Дашборд панели. |
| `permissions.permission-manager` | CRUD-панель Разрешений. |
| `roles.role-manager` | CRUD-панель Ролей, включая ceiling-роли. |
| `guards.guard-manager` | CRUD-панель Guards. |
| `settings.settings-manager` | Панель Настроек (метаданные отображения, иконки, цвета). |
| `users.user-clearance-manager` | Панель назначения ролей/разрешений для отдельного пользователя. Принимает `:userId`. |

```blade
<livewire:clearance::dashboard />

<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

См. соответствующие страницы функций для каждой панели: [Дашборд](/ru/features/dashboard.md), [Разрешения](/ru/features/permissions.md), [Роли](/ru/features/roles.md), [Guards](/ru/features/guards.md), [Настройки](/ru/features/settings.md), [Пользователи](/ru/features/users.md).

::: tip
Ключ `config('clearance.ui.flux_pro')` управляет тем, рендерятся ли в этих компонентах элементы Flux UI, доступные только в Pro-версии. `null` (значение по умолчанию) автоматически определяет наличие Flux Pro через `Flux::pro()`.
:::
