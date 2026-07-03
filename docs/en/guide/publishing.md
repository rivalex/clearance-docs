# Publishing

Clearance publishes three kinds of assets via `vendor:publish` tags: config, migrations, and translations. Config and migrations are handled automatically by the installer (see [Installation](/en/guide/installation)); this page covers publishing them manually, and publishing translations.

## Config

```bash
php artisan vendor:publish --tag=clearance-config
```

Publishes `config/clearance.php` to your application. See the [Configuration reference](/en/guide/configuration) for every key, its default, and its purpose.

## Translations

```bash
php artisan vendor:publish --tag=clearance-translations
```

This copies the 9 bundled languages (`ar`, `en`, `es`, `fr`, `hi`, `it`, `pt`, `ru`, `zh`) to `lang/vendor/clearance/{locale}/ui.php` in your app. To add a new locale or override strings in an existing one, add a locale directory there, using `en/ui.php` as the canonical key reference. All locales expose an identical key set, verified by a dedicated test on every change.

## Overriding the panel layout

The `layout` config key controls the Blade layout used by full-page components:

```php
// config/clearance.php
'layout' => null, // host app's default (config('livewire.layout'))
```

Leave it `null` to use your application's default Livewire layout, or set it to a specific layout view to render the panel inside a custom shell.

## Migrations

Clearance's migrations are published and run automatically by `php artisan clearance:install` - the installer also publishes and runs Spatie's migrations first if the `roles` table doesn't exist yet. See [Installation](/en/guide/installation) for the full step-by-step flow, and the [danger callout](/en/guide/installation#run-the-installer) about never running a plain `migrate` on a fresh setup.

## Related

- [Configuration reference](/en/guide/configuration) - every config key
- [Installation](/en/guide/installation) - the full installer flow
- [Migration](/en/guide/migration) - `clearance:backfill` for existing Spatie installs
