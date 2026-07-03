# Rôles

Le panneau Rôles est une interface CRUD complète sur le modèle `Role` de Spatie, étendue avec deux concepts propres à Clearance : la **portée** (global ou contextuel) et le **plafond** (un rôle parent qui limite les permissions d'un enfant).

## Ce que fait le panneau

- **Créer / modifier / supprimer** des rôles.
- **Attribution de permissions limitée à la garde** - assigner des permissions à un rôle, filtrées selon la garde du rôle.
- **Sélection de la portée** - `global` ou `contextual`.
- **Configuration du plafond** - définir ou retirer un rôle parent.

<Screenshot src="/screenshots/roles-page.png" alt="Panneau Rôles de Clearance listant les rôles avec leur portée et plafond" caption="Le panneau Rôles liste chaque rôle avec sa portée et, le cas échéant, son parent plafond." />

## Portée : global vs. contextuel

Chaque rôle déclare une portée à sa création :

- **`global`** - assigné directement à un utilisateur via `$user->assignRole()`, s'applique partout.
- **`contextual`** - lié à un ou plusieurs types de modèle (`context_types`), assignable uniquement au sein d'une instance de contexte spécifique via `UserRoleContext`. Voir [Autorisation contextuelle](/fr/features/contextual-authorization) et [Utilisateurs](/fr/features/users#contextual-roles-under-the-hood).

Les rôles sans ligne `RoleMeta` explicite ont par défaut la portée `global`, pour la rétrocompatibilité.

## Rôles plafonds

Un rôle peut déclarer un plafond - un rôle parent dont l'ensemble de permissions agit comme une limite supérieure :

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
```

<Screenshot src="/screenshots/role-form-ceiling.png" alt="Formulaire de rôle avec un rôle parent plafond sélectionné" caption="Définir un parent plafond dans le formulaire de rôle (RoleForm)." />

Règles appliquées automatiquement à chaque enregistrement :

- L'enfant ne peut détenir que des permissions que le parent détient également. Tout excédent est silencieusement retiré lors de `setParent()` et à chaque synchronisation de permission ultérieure - aucune exception, aucun message d'erreur, simplement appliqué silencieusement.
- Un rôle qui agit déjà comme parent ne peut pas devenir enfant (`ClearanceScopeViolationException`).
- Un rôle qui est déjà enfant ne peut pas lui-même agir comme parent (`ClearanceScopeViolationException`).
- Retirer des permissions d'un parent propage automatiquement la suppression à chaque enfant.

::: warning Un plafond ne fait que retirer, il n'accorde jamais
Définir un parent permissif n'accorde rien à l'enfant par lui-même. Le plafond est strictement une limite supérieure - il n'ajoute jamais de permissions à l'enfant, il ne peut que les retirer.
:::

Ceci se configure par rôle dans le panneau Rôles (`RoleForm`), ou par programmation via `RoleService::setParent()` / `RoleService::removeParent()`.

## Contrôle d'accès

L'accès en lecture nécessite `clearance-access`. Créer, modifier, ou supprimer un rôle - y compris ses attributions de permissions, sa portée, et son plafond - nécessite `clearance-roles-write` :

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

## Route et composant intégré

| URI | Nom de route | Description |
|---|---|---|
| `/clearance/roles` | `clearance.roles` | CRUD des rôles avec attribution de permissions limitée à la garde, portée (global/contextuel), et plafond |

```blade
<livewire:clearance::roles.role-manager />
```

## Base de données

Les métadonnées de rôle (portée, plafond, état de verrouillage) vivent dans `clr_role_meta`, jamais dans la table `roles` de Spatie :

| Table | Colonnes clés |
|---|---|
| `clr_role_meta` | `role_id` (unique, FK -> `roles`) - `is_locked`, `scope` (`global`\|`contextual`), `context_types` (json) - `parent_role_id` (FK nullable -> `roles`, `ON DELETE SET NULL`) |

## Prochaines étapes

- [Autorisation contextuelle](/fr/features/contextual-authorization) - comment les rôles `scope=contextual` sont vérifiés à l'exécution.
- [Utilisateurs](/fr/features/users) - assigner des rôles à un utilisateur spécifique, globalement ou par contexte.
- [Référence des services](/fr/api/services) - API complète de `RoleService`.
