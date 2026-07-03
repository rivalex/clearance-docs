# Миграция с существующей установки Spatie

Если ваше приложение уже использует `spatie/laravel-permission` с существующими ролями, разрешениями и назначениями, `clearance:install` обнаруживает и сохраняет их - он никогда не запускает миграции Spatie, если таблица `roles` уже существует. См. [Установку](/ru/guide/installation) для полного процесса установки.

После установки используйте `clearance:backfill`, чтобы применить собственные метаданные Clearance к вашим существующим ролям и guard'ам.

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

## Что делает каждый раздел

| Раздел | Эффект |
|---|---|
| `meta` | Заполняет отображаемое имя `clr_meta` (роль в стиле Title Case) для каждой роли, у которой его ещё нет. |
| `roles` | Заполняет значения по умолчанию `clr_role_meta` (`scope=global`, `is_locked=false`) для каждой роли, у которой их ещё нет. |
| `guards` | Импортирует guard'ы из `config('auth.guards')` в `clr_guards`, отфильтрованные по `allowed_guard_drivers`. |

Используйте `--only=<section>`, чтобы выполнить только один раздел вместо всех трёх.

::: tip Идемпотентно
Все разделы идемпотентны - повторный запуск `clearance:backfill` безопасен и не приведёт к дублированию или перезаписи существующих строк `clr_*`.
:::

## Граничные случаи

В репозитории пакета есть более подробное руководство по миграции, охватывающее граничные случаи, не описанные здесь - в частности, что происходит, если в вашей базе данных уже есть роль с именем `super_admin` (или алиасом вроде `superadmin`, `root`, `owner`), которая конфликтует с собственной ролью `super_admin` Clearance, а также заметки о пользовательских подклассах моделей `Role`/`Permission` и поведении импорта guard'ов. Прочитайте его перед выполнением backfill для продакшн-базы данных с нетривиальными существующими данными о ролях. См. также [Super Admin](/ru/features/super-admin) для поведения обнаружения алиасов во время `clearance:install`.

## Смотрите также

- [Установка](/ru/guide/installation) - полный процесс `clearance:install`
- [Команды Artisan](/ru/api/artisan-commands) - полный справочник команд
- [Архитектура](/ru/guide/architecture) - таблицы `clr_*`, которые заполняет backfill
