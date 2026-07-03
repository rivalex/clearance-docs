# Utilisateurs

Le module Utilisateurs est un panneau par utilisateur permettant d'assigner des rôles - globalement ou limités à un contexte spécifique - et d'accorder ou de refuser des permissions individuelles au sein d'un contexte. Il est optionnel et désactivé par défaut.

## Activer le module

```php
// config/clearance.php
'modules' => ['users' => true],
```

## Ce que fait le panneau

- **Attribution de rôle globale** - assigner des rôles `scope=global` directement à l'utilisateur, à l'échelle de l'application.
- **Attribution de rôle contextuelle** - assigner des rôles `scope=contextual` limités à une instance de modèle spécifique (un Store, Team, ou Project déclaré dans `contextual_models`).
- **Surcharges de permission par contexte** - pour chaque attribution de rôle contextuelle, des permissions supplémentaires peuvent être forcées en accordé ou en refusé, indépendamment par instance de contexte.
- **Retirer une attribution** - retire un rôle de l'utilisateur (portée globale ou contextuelle), en propageant la suppression de toute surcharge associée.

<Screenshot src="/screenshots/user-panel-global.png" alt="Panneau utilisateur montrant une attribution de rôle globale" caption="Attribution d'un rôle global à un utilisateur - s'applique à toute l'application." />

<Screenshot src="/screenshots/user-panel-contextual.png" alt="Panneau utilisateur montrant une attribution de rôle contextuelle et des surcharges de permission par contexte" caption="Attribution d'un rôle contextuel et définition de surcharges de permission par contexte pour une instance de Store spécifique." />

::: warning Plafond de privilège sur l'auto-modification
L'attribution de rôle comme les attributions de permission directes/par surcharge appliquent toutes deux un plafond de privilège : un acteur non-`super_admin` ne peut pas modifier ses propres attributions, et ne peut pas accorder une permission qu'il ne détient pas lui-même.
:::

## Contrôle d'accès

L'accès à `/clearance/user/{userId}` nécessite `clearance-access` (lecture) et `clearance-users-write` (attribution/retrait) :

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-users-write');
```

## Route et composant intégré

| URI | Nom de route | Description |
|---|---|---|
| `/clearance/user/{userId}` | `clearance.user` | Attribution de rôle par utilisateur + surcharges de permissions contextuelles (nécessite `modules.users = true`) |

```blade
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

### Créer un lien vers le panneau depuis votre application

```php
route('clearance.user', ['userId' => $user->id])
```

## Rôles contextuels sous le capot

Assigner un rôle contextuel à un utilisateur crée une ligne `UserRoleContext` :

```php
use Rivalex\Clearance\Models\UserRoleContext;

// Samuele is "StoreStaff" only within Alessandro's Store
UserRoleContext::create([
    'user_id'      => $samuele->id,
    'role_id'      => $staffRole->id,
    'context_type' => Store::class,
    'context_id'   => $store->id,
]);
```

Les contrôles d'autorisation respectent le contexte via `$user->canIn()`, `@canin`, et `ContextService`. Voir [Autorisation contextuelle](/fr/features/contextual-authorization) pour le modèle de résolution complet.

## Base de données

| Table | Colonnes clés |
|---|---|
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - unique par `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) |

## Prochaines étapes

- [Autorisation contextuelle](/fr/features/contextual-authorization) - l'ordre de priorité de résolution complet pour `canIn()`.
- [Rôles](/fr/features/roles) - configurer la portée d'un rôle et ses `context_types`.
- [Référence de configuration](/fr/guide/configuration) - `modules.users`, `contextual_models`.
