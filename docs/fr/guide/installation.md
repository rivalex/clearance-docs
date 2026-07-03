# Installation

## Prérequis

Consultez le [tableau des prérequis](/fr/guide/introduction#requirements) complet sur la page Introduction - PHP ^8.3, Laravel ^11.0|^12.0|^13.0, spatie/laravel-permission ^6.0, livewire/livewire ^3.0|^4.0, livewire/flux ^2.14.

## Installer le package

```bash
composer require rivalex/clearance
```

## Lancer l'installateur

```bash
php artisan clearance:install
```

::: danger Exécutez toujours clearance:install, jamais un simple migrate
Les migrations propres à Clearance dépendent de l'existence préalable des tables `roles`/`permissions` de Spatie ; l'installateur publie et exécute les migrations de Spatie avant les siennes. Si vous exécutez directement `php artisan migrate` sur une installation neuve alors que Spatie n'est pas encore configuré, vous obtiendrez un message d'erreur explicite vous indiquant d'exécuter l'installateur à la place.
:::

### Options facultatives

| Option | Description |
|---|---|
| `--user=ID` | Assigne le rôle `super_admin` à un utilisateur après l'installation |
| `--role=NAME` | Assigne `clearance-access` à un rôle (créé s'il est absent) |
| `--super-admin-role=NAME` | Promeut un rôle existant en `super_admin` (évite l'invite interactive) |
| `--force` | Relance même si déjà installé |

### Ce que fait l'installateur

1. Publie `config/clearance.php`.
2. Publie les migrations de Clearance.
3. Détecte les tables Spatie Permission - si la table `roles` est absente, publie et exécute d'abord automatiquement les migrations de Spatie.
4. Exécute toutes les migrations en attente.
5. Crée `clearance-access` ainsi que les 5 permissions de capacité fines : `clearance-permissions-write`, `clearance-roles-write`, `clearance-guards-write`, `clearance-settings-write`, `clearance-users-write`.
6. Crée le rôle `super_admin` avec toutes les permissions `clearance-*` assignées (additif - ne retire jamais de permissions existantes lors d'une relance).
7. Écrit `storage/.clearance-installed` comme marqueur d'idempotence.

::: tip Migration depuis une installation Spatie existante ?
Utilisez `php artisan clearance:backfill` plutôt que de repartir de zéro, et lisez d'abord le [guide de migration](/fr/guide/migration) - il couvre la collision avec `super_admin`, les sous-classes de modèle personnalisées, et l'import des gardes.
:::

## Ajouter HasClearance à votre modèle User

Pour utiliser les méthodes d'autorisation contextuelle (`$user->canIn()`, `$user->hasRoleIn()`), ajoutez le trait `HasClearance` à `App\Models\User`. Il inclut déjà `HasRoles` de Spatie - une seule ligne remplace les deux :

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance; // includes HasRoles + all contextual authorization methods
}
```

::: tip Vous utilisez déjà `use HasRoles` ?
Remplacez-le par `use HasClearance`, ou gardez les deux - PHP dédoublonne automatiquement la composition de traits, sans conflit.
:::

Sans ce trait, `$user->canIn()` et les méthodes associées ne sont pas disponibles, mais les directives Blade (`@canin`, `@hasrolein`) et `ContextService` continuent de fonctionner dans tous les cas - elles ne dépendent pas du trait. Voir [Autorisation contextuelle](/fr/features/contextual-authorization) pour plus de détails.

## Prochaines étapes

Continuez avec le [Démarrage rapide](/fr/guide/quick-start) pour monter le panneau et commencer à gérer les permissions.
