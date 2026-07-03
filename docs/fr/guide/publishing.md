# Publication

Clearance publie trois types d'assets via des tags `vendor:publish` : la configuration, les migrations, et les traductions. La configuration et les migrations sont gérées automatiquement par l'installateur (voir [Installation](/fr/guide/installation)) ; cette page couvre leur publication manuelle, ainsi que la publication des traductions.

## Configuration

```bash
php artisan vendor:publish --tag=clearance-config
```

Publie `config/clearance.php` dans votre application. Voir la [référence de configuration](/fr/guide/configuration) pour chaque clé, sa valeur par défaut, et son rôle.

## Traductions

```bash
php artisan vendor:publish --tag=clearance-translations
```

Ceci copie les 9 langues incluses (`ar`, `en`, `es`, `fr`, `hi`, `it`, `pt`, `ru`, `zh`) vers `lang/vendor/clearance/{locale}/ui.php` dans votre application. Pour ajouter une nouvelle locale ou surcharger des chaînes dans une locale existante, ajoutez-y un répertoire de locale, en utilisant `en/ui.php` comme référence de clés canonique. Toutes les locales exposent un ensemble de clés identique, vérifié par un test dédié à chaque changement.

## Surcharger le layout du panneau

La clé de configuration `layout` contrôle le layout Blade utilisé par les composants pleine page :

```php
// config/clearance.php
'layout' => null, // host app's default (config('livewire.layout'))
```

Laissez-la à `null` pour utiliser le layout Livewire par défaut de votre application, ou définissez-la sur une vue de layout spécifique pour rendre le panneau dans une coquille personnalisée.

## Migrations

Les migrations de Clearance sont publiées et exécutées automatiquement par `php artisan clearance:install` - l'installateur publie et exécute également d'abord les migrations de Spatie si la table `roles` n'existe pas encore. Voir [Installation](/fr/guide/installation) pour le flux détaillé complet, et l'[encadré de danger](/fr/guide/installation#run-the-installer) concernant le fait de ne jamais exécuter un simple `migrate` sur une installation neuve.

## Voir aussi

- [Référence de configuration](/fr/guide/configuration) - chaque clé de configuration
- [Installation](/fr/guide/installation) - le flux complet de l'installateur
- [Migration](/fr/guide/migration) - `clearance:backfill` pour les installations Spatie existantes
