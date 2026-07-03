# Facade

La facade `Clearance` (`Rivalex\Clearance\Facades\Clearance`) expose l'API d'autorisation contextuelle du package sous forme d'appels de type statique. Elle est adossée à `src/Clearance.php`, qui délègue chaque appel à [`ContextService`](/fr/api/services.md#contextservice).

```php
use Rivalex\Clearance\Facades\Clearance;
```

::: tip
Les directives Blade et `ContextService` fonctionnent sans aucun changement à votre modèle `User`. La facade est un wrapper de confort autour du même moteur — voir [Autorisation contextuelle](/fr/features/contextual-authorization.md) pour le concept derrière les vérifications limitées au contexte.
:::

## `canIn()`

Vérifie si un utilisateur détient une permission limitée à une instance de contexte.

```php
Clearance::canIn($user, 'orders-create', $store);      // bool
Clearance::canIn($user, 'orders-create', $store, 'api'); // optional 3rd arg: guard name filter
```

| Paramètre | Type | Description |
|---|---|---|
| `$user` | `Authenticatable` | L'utilisateur à vérifier. |
| `string $permission` | `string` | Nom de la permission, ex. `orders-create`. |
| `$context` | instance de modèle | Le contexte auquel la permission est limitée, ex. un `Store`. |
| `?string $guard` | `string\|null` | Filtre optionnel de nom de garde. |

Renvoie `bool`.

## `hasPermissionIn()`

Alias de [`canIn()`](#canin), nommé pour refléter la convention `hasPermissionTo()` de Spatie.

```php
Clearance::hasPermissionIn($user, 'orders-create', $store);
```

Renvoie `bool`.

## `hasRoleIn()`

Vérifie si un utilisateur détient un rôle limité à une instance de contexte.

```php
Clearance::hasRoleIn($user, 'manager', $store);
```

Renvoie `bool`.

## `resolveFor()`

Résout chaque permission effective qu'un utilisateur détient au sein d'un contexte donné.

```php
Clearance::resolveFor($user, $store); // Collection<string>
```

Renvoie une `Collection<string>` de noms de permissions. Voir [`ContextService::resolveFor()`](/fr/api/services.md#contextservice) pour les règles de priorité des 3 sources utilisées pour construire cette liste.

## `guards()`

Liste les noms de toutes les gardes d'authentification gérées par Clearance.

```php
Clearance::guards(); // Collection<string>
```

Renvoie une `Collection<string>`. Adossée à [`GuardService`](/fr/api/services.md#guardservice). Voir la [page de la fonctionnalité Gardes](/fr/features/guards.md) pour la configuration des gardes et leur injection dans `auth.guards`.

## Voir aussi

- [Traits & Directives](/fr/api/traits-directives.md) — les méthodes d'instance équivalentes via `HasClearance`, et les directives Blade sûres pour les invités.
- [Services](/fr/api/services.md) — le `ContextService` sous-jacent et les services associés.
