# Установка

## Требования

Полную [таблицу требований](/ru/guide/introduction#requirements) смотрите на странице «Введение» - PHP ^8.3, Laravel ^11.0|^12.0|^13.0, spatie/laravel-permission ^6.0, livewire/livewire ^3.0|^4.0, livewire/flux ^2.14.

## Установка пакета

```bash
composer require rivalex/clearance
```

## Запуск установщика

```bash
php artisan clearance:install
```

::: danger Всегда запускайте clearance:install, никогда не запускайте обычный migrate
Собственные миграции Clearance зависят от того, что таблицы `roles`/`permissions` Spatie уже существуют; установщик публикует и запускает миграции Spatie перед своими собственными. Если вы запустите `php artisan migrate` напрямую на новой установке, где Spatie ещё не настроен, вы получите понятное сообщение об ошибке с указанием запустить вместо этого установщик.
:::

### Опциональные флаги

| Флаг | Описание |
|---|---|
| `--user=ID` | Назначить роль `super_admin` пользователю после установки |
| `--role=NAME` | Назначить `clearance-access` роли (создаётся, если отсутствует) |
| `--super-admin-role=NAME` | Повысить существующую роль до `super_admin` (пропускает интерактивный запрос) |
| `--force` | Повторный запуск, даже если уже установлено |

### Что делает установщик

1. Публикует `config/clearance.php`.
2. Публикует миграции Clearance.
3. Определяет наличие таблиц Spatie Permission - если таблица `roles` отсутствует, сначала автоматически публикует и запускает миграции Spatie.
4. Выполняет все ожидающие миграции.
5. Создаёт `clearance-access` и все 5 точечных разрешений: `clearance-permissions-write`, `clearance-roles-write`, `clearance-guards-write`, `clearance-settings-write`, `clearance-users-write`.
6. Создаёт роль `super_admin` со всеми назначенными разрешениями `clearance-*` (аддитивно - никогда не удаляет существующие разрешения при повторном запуске).
7. Записывает `storage/.clearance-installed` как маркер идемпотентности.

::: tip Мигрируете с уже существующей установки Spatie?
Используйте `php artisan clearance:backfill` вместо установки с нуля и сначала прочитайте [руководство по миграции](/ru/guide/migration) - в нём описаны коллизия `super_admin`, пользовательские подклассы моделей и импорт guard'ов.
:::

## Добавьте HasClearance в модель User

Чтобы использовать методы контекстной авторизации (`$user->canIn()`, `$user->hasRoleIn()`), добавьте трейт `HasClearance` в `App\Models\User`. Он уже включает `HasRoles` из Spatie - одна строка заменяет оба трейта:

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance; // includes HasRoles + all contextual authorization methods
}
```

::: tip Уже используете `use HasRoles`?
Замените его на `use HasClearance`, или оставьте оба - PHP автоматически дедуплицирует композицию трейтов, конфликтов не будет.
:::

Без этого трейта `$user->canIn()` и связанные с ним методы недоступны, но директивы Blade (`@canin`, `@hasrolein`) и `ContextService` продолжают работать в любом случае - они не зависят от трейта. См. [Контекстную авторизацию](/ru/features/contextual-authorization) для подробностей.

## Следующие шаги

Переходите к [Быстрому старту](/ru/guide/quick-start), чтобы подключить панель и начать управлять разрешениями.
