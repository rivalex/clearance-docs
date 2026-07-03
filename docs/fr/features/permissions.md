# Permissions

Le panneau Permissions est une interface CRUD complète sur le modèle `Permission` de Spatie. Clearance n'introduit jamais sa propre table de permissions - chaque permission que vous créez ici est une permission Spatie ordinaire, utilisable partout où l'API de Spatie est utilisée (`$user->can()`, `Gate::allows()`, policies, `@can`).

## Ce que fait le panneau

- **Créer** - ajouter une nouvelle permission, validée selon la [convention de nommage](#permission-naming-convention) (sauf si désactivée) et limitée à une garde.
- **Modifier** - renommer une permission ou changer sa garde.
- **Supprimer** - retirer une permission et ses attributions de rôle/utilisateur (comportement de cascade Spatie standard).

<Screenshot src="/screenshots/permissions-page.png" alt="Panneau Permissions de Clearance listant les permissions groupées par préfixe de nommage" caption="Le panneau Permissions liste chaque permission Spatie, groupée par préfixe de nommage." />

<Screenshot src="/screenshots/permission-form-modal.png" alt="Formulaire de création/modification de permission avec validation de la convention de nommage" caption="Le formulaire de permission valide la convention de nommage group-action avant l'enregistrement." />

## Convention de nommage des permissions

Les permissions doivent suivre le format `group-action` :

```
orders-create       correct
orders-read         correct
store-orders-delete correct

create              wrong (bare action, no group)
orders.create       wrong (dot separator)
Orders-Create       wrong (camelCase)
```

Le préfixe de groupe (`orders` dans `orders-create`) est ce que le [Tableau de bord](/fr/features/dashboard) utilise pour compter les groupes de permissions distincts.

::: tip Désactiver le contrôle
Certaines équipes migrent depuis un schéma de nommage différent. Désactivez le contrôle par environnement :

```php
// config/clearance.php
'enforce_naming_convention' => false,
```
:::

## Contrôle d'accès

L'accès en lecture au panneau (visualiser la liste des permissions) nécessite `clearance-access`. Créer, modifier, ou supprimer une permission nécessite en plus `clearance-permissions-write` :

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-permissions-write');
```

::: warning Les permissions `clearance-*` sont réservées
Les permissions `clearance-*` elles-mêmes (`clearance-access`, `clearance-permissions-write`, etc.) ne peuvent pas être créées, renommées, ou supprimées via ce panneau - même par un acteur `super_admin`. Elles sont gérées exclusivement par le propre flux d'installation du package. Voir [Super Admin](/fr/features/super-admin#reserved-clearance-permissions).
:::

## Route et composant intégré

| URI | Nom de route | Description |
|---|---|---|
| `/clearance/permissions` | `clearance.permissions` | CRUD des permissions avec validation du nommage par préfixe de groupe |

```blade
<livewire:clearance::permissions.permission-manager />
```

## Prochaines étapes

- [Rôles](/fr/features/roles) - assigner des permissions à un rôle, limitées à la garde.
- [Super Admin](/fr/features/super-admin) - le tableau complet des permissions d'écriture fines.
- [Référence de configuration](/fr/guide/configuration) - `enforce_naming_convention`, `naming_separator`.
