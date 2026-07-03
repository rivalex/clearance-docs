# Services

Tous les services de Clearance vivent dans `src/Services/` et sont résolus depuis le conteneur, ex. `app(ContextService::class)`. Ils constituent la couche à privilégier depuis les contrôleurs, policies, et jobs — partout où le modèle `User` peut ne pas utiliser le trait `HasClearance`.

## `ContextService`

`Rivalex\Clearance\Services\ContextService` (`src/Services/ContextService.php`) est le moteur derrière l'autorisation contextuelle. La [facade `Clearance`](/fr/api/facade.md) et le trait `HasClearance` délèguent tous deux vers lui.

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

`resolveFor()` construit son résultat selon une priorité à 3 sources :

1. Attributions de rôle contextuelles
2. Attributions de rôle globales
3. Surcharges par contexte (`forced_on` ajoute, `forced_off` l'emporte toujours)

Voir [Autorisation contextuelle](/fr/features/contextual-authorization.md) pour la présentation conceptuelle de cette priorité.

## `RoleService`

`Rivalex\Clearance\Services\RoleService` (`src/Services/RoleService.php`) gère les rôles plafonds.

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
app(RoleService::class)->removeParent($childRole);
```

| Méthode | Description |
|---|---|
| `setParent($childRole, $parentRole)` | Définit un plafond : les permissions excédentaires sur `$childRole` au-delà de l'ensemble du parent sont silencieusement retirées. Lève `ClearanceScopeViolationException` si l'enfant est déjà un parent, ou si le parent est déjà un enfant. |
| `removeParent($childRole)` | Retire la relation de plafond. |

::: warning
Un plafond ne fait que retirer — il n'accorde jamais. Un rôle parent permissif ne donne, par lui-même, aucune permission à l'enfant. Retirer une permission d'un rôle parent propage automatiquement cette suppression à chaque rôle enfant.
:::

Voir [Rôles](/fr/features/roles.md) pour le concept de rôle plafond en détail.

## `PermissionService`

`Rivalex\Clearance\Services\PermissionService` (`src/Services/PermissionService.php`) alimente le CRUD du panneau Permissions.

Il applique la convention de nommage `group-action`, contrôlée par les clés de configuration `enforce_naming_convention` et `naming_separator`, et protège l'espace de noms de permission `clearance-*` contre toute création, renommage, ou suppression en dehors du flux d'installation.

## `GuardService`

`Rivalex\Clearance\Services\GuardService` (`src/Services/GuardService.php`) alimente le CRUD du panneau Gardes.

Il injecte les gardes stockés en base de données dans `auth.guards` au démarrage, filtrés par la clé de configuration `allowed_guard_drivers` — un driver non listé est ignoré avec un avertissement dans les logs plutôt que de corrompre la configuration d'authentification.

## `MetaService`

`Rivalex\Clearance\Services\MetaService` (`src/Services/MetaService.php`) lit et écrit `clr_meta` (nom d'affichage, description, icône SVG assainie, couleur) pour un sujet rôle ou garde. Il alimente l'éditeur de métadonnées d'affichage du panneau Paramètres.

Les icônes SVG passent par `Rivalex\Clearance\Support\SvgSanitizer` (un assainisseur en liste blanche) à la fois avant le stockage et au moment du rendu.

## `UserClearanceService`

`Rivalex\Clearance\Services\UserClearanceService` (`src/Services/UserClearanceService.php`) alimente le panneau du module Utilisateurs :

- Attribution de rôle globale
- Attribution de rôle contextuelle (via `UserRoleContext`)
- Surcharges de permission par contexte (via `UserContextPermissionOverride`, `forced_on` / `forced_off`)
- Retrait d'attributions, ce qui propage la suppression des surcharges

Il applique un plafond de privilège : un acteur non-`super_admin` ne peut pas modifier ses propres attributions, et ne peut pas accorder une permission qu'il ne détient pas lui-même. Voir [Super Admin](/fr/features/super-admin.md) et [Utilisateurs](/fr/features/users.md).

## Exceptions

Toutes dans `src/Exceptions/` :

| Exception | Levée quand |
|---|---|
| `ClearanceConfigException` | Une valeur invalide ou mal configurée de `config/clearance.php` est lue (ex. un `naming_separator` incorrect). |
| `ClearanceNamingException` | Un nom de permission viole la convention de nommage `group-action` alors que le contrôle est activé. |
| `ClearanceProtectedResourceException` | Une tentative est faite de modifier une permission `clearance-*` protégée, ou une ressource verrouillée, en dehors du flux d'installation. |
| `ClearanceScopeViolationException` | Une relation de rôle plafond invalide (un parent essayant de devenir enfant, ou inversement), ou une incohérence de portée. |

## Voir aussi

- [Facade](/fr/api/facade.md) — le wrapper en appel statique autour de `ContextService`.
- [Traits & Directives](/fr/api/traits-directives.md) — les équivalents en méthode d'instance et directive Blade.
- [Commandes Artisan](/fr/api/artisan-commands.md) — `clearance:install` et `clearance:backfill`, qui amorcent les données sur lesquelles ces services opèrent.
