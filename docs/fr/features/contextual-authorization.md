# Autorisation contextuelle

Clearance étend le modèle d'autorisation global de Spatie avec des vérifications limitées au contexte. Les rôles d'un utilisateur peuvent être assignés au sein d'un contexte spécifique (par ex. un Store, Tenant, ou Project) et sont invisibles en dehors de cette portée. C'est le mécanisme central derrière le [module Utilisateurs](/fr/features/users) et les [Rôles](/fr/features/roles#scope-global-vs-contextual) contextuels.

## Sur le modèle User

Disponible lorsque votre modèle `User` utilise le trait `HasClearance` (voir [Installation](/fr/guide/installation#add-hasclearance-to-your-user-model)) :

```php
// Does the user have this permission in this context?
$user->canIn('orders-create', $store);

// Alias - mirrors Spatie's hasPermissionTo() naming
$user->hasPermissionIn('orders-create', $store);

// Does the user hold this role in this context?
$user->hasRoleIn('manager', $store);

// Optionally filter by guard
$user->canIn('orders-create', $store, 'api');
$user->hasRoleIn('manager', $store, 'web');
```

## Dans les vues Blade

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

::: tip Sûr pour les invités par conception
Les deux directives se résolvent de façon sûre (renvoient `false`) pour un utilisateur invité - elles ne lèvent jamais d'exception. Vous pouvez utiliser `@canin`/`@hasrolein` dans des vues rendues avant que l'authentification ne soit garantie, sans les envelopper dans une vérification `@auth`.
:::

## Via ContextService

Pour une utilisation dans des contrôleurs, policies, ou jobs où le modèle `User` peut ne pas avoir le trait `HasClearance` :

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

## Via la facade Clearance

La facade `Clearance` délègue au même service - un point d'entrée global pratique :

```php
Clearance::canIn($user, 'orders-create', $store);
Clearance::resolveFor($user, $store);
Clearance::guards(); // names of all managed authentication guards
```

Voir la [référence de la facade](/fr/api/facade) pour la liste complète des méthodes.

## Comment fonctionne la résolution

`resolveFor()` fusionne trois sources, dans cet ordre de priorité :

1. **Attributions de rôle contextuelles** - permissions issues des rôles que l'utilisateur détient spécifiquement dans ce contexte (`clr_role_ctx`).
2. **Attributions de rôle globales** - permissions issues des rôles Spatie assignés globalement à l'utilisateur (ainsi `super_admin` et les autres rôles globaux satisfont `canIn()` pour n'importe quel contexte).
3. **Surcharges par contexte** (`clr_ctx_overrides`) - `forced_on` ajoute une permission en plus de ce qui précède ; `forced_off` la retire, même si la permission a été accordée par un rôle global.

::: danger Un refus l'emporte toujours
Si une permission est `forced_off` pour un utilisateur dans un contexte donné, ce refus prime sur toute autre source - y compris une attribution de rôle global et une attribution de rôle contextuelle. Il n'existe aucun moyen de réaccorder une permission `forced_off` pour ce contexte, sinon en supprimant la surcharge elle-même. Concevez votre politique de surcharge en gardant cela à l'esprit : `forced_off` est le signal le plus fort du système.
:::

## Base de données

| Table | Colonnes clés |
|---|---|
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - unique par `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) |

## Prochaines étapes

- [Utilisateurs](/fr/features/users) - l'interface du panneau pour assigner des rôles contextuels et des surcharges.
- [Rôles](/fr/features/roles#scope-global-vs-contextual) - déclarer un rôle comme `contextual` et ses `context_types` autorisés.
- [Référence Traits & Directives](/fr/api/traits-directives) - signatures complètes du trait `HasClearance` et des directives Blade.
- [Référence des services](/fr/api/services) - API complète de `ContextService`.
