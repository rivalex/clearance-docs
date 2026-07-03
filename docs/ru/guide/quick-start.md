# Быстрый старт

После [установки](/ru/guide/installation) у вас есть два способа открыть доступ к панели Clearance: готовые маршруты или встраивание отдельных Livewire-компонентов в любом месте приложения.

## Вариант A - Готовые маршруты

После установки панель доступна по адресу `/clearance` (или настроенному `route_prefix`, см. [Конфигурацию](/ru/guide/configuration)). Все маршруты защищены middleware `clearance.access` и автоматически используют Livewire-layout основного приложения.

| URI | Имя маршрута | Описание |
|---|---|---|
| `/clearance` | `clearance.home` | Дашборд |
| `/clearance/guards` | `clearance.guards` | Управление guard'ами (создание/редактирование/удаление) |
| `/clearance/permissions` | `clearance.permissions` | CRUD разрешений с валидацией именования по префиксу группы |
| `/clearance/roles` | `clearance.roles` | CRUD ролей с назначением разрешений в рамках guard'а, областью (глобальная/контекстная) и ceiling |
| `/clearance/settings` | `clearance.settings` | Метаданные отображения (иконки, цвета), роль по умолчанию, общие настройки |
| `/clearance/user/{userId}` | `clearance.user` | Назначение ролей для отдельного пользователя + контекстные переопределения разрешений (требует `modules.users = true`) |

Имена маршрутов остаются стабильными независимо от изменений префикса. Чтобы настроить middleware:

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

## Вариант B - Встроенные Livewire-компоненты

Каждый раздел панели можно подключить отдельно в любом Blade-представлении, странице Filament или собственном маршруте:

```blade
<livewire:clearance::dashboard />
<livewire:clearance::permissions.permission-manager />
<livewire:clearance::roles.role-manager />
<livewire:clearance::guards.guard-manager />
<livewire:clearance::settings.settings-manager />
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

## Что дальше

- [Справочник конфигурации](/ru/guide/configuration) - каждый ключ конфигурации, значение по умолчанию и назначение
- [Контекстная авторизация](/ru/features/contextual-authorization) - привязка ролей и разрешений к Store, Tenant или Project
- [Роли](/ru/features/roles) - область и ceiling-роли
- [Дашборд](/ru/features/dashboard) - что показывает компонент Дашборда
- [Пользователи](/ru/features/users) - опциональная панель для отдельного пользователя
