# Super Admin

Le rôle `super_admin` est créé automatiquement par `clearance:install` et maintenu synchronisé avec toutes les permissions `clearance-*`.

```bash
# Install and make user ID 1 a super admin
php artisan clearance:install --user=1
```

Voir le [guide d'installation](/fr/guide/installation) et la [référence des commandes Artisan](/fr/api/artisan-commands) pour la liste complète des options de l'installateur.

## Contournement de Gate (optionnel)

`super_admin_gate_bypass` contrôle si le rôle `super_admin` contourne toutes les vérifications Laravel `can()` / `Gate::allows()` / policy à l'échelle globale, via un hook `Gate::before()`. Défaut : `false`. Le contournement est optionnel. Pour l'activer :

```php
// config/clearance.php
'super_admin_gate_bypass' => true,
```

::: danger À l'échelle de l'application, pas limité au panneau
Une fois activé, tout utilisateur détenant `super_admin` contourne **toutes** les vérifications de Gate à l'échelle de l'application - pas seulement les vérifications du panneau Clearance. Cela affecte chaque `can()`, `Gate::allows()`, et vérification de policy dans votre application, pas uniquement ceux que Clearance définit. Pour une portée plus restreinte, laissez-le désactivé et accordez les permissions explicitement à la place.
:::

## Détection d'alias

Si votre base de données contient déjà un rôle nommé `super_admin`, `superadmin`, `super-admin`, `root`, ou `owner`, l'installateur le détecte et vous invite de façon interactive à le promouvoir ou à créer un `super_admin` séparé. Utilisez `--super-admin-role=NAME` pour éviter l'invite :

```bash
php artisan clearance:install --super-admin-role=owner
```

::: tip La promotion est non destructive
La promotion renomme le rôle en place - toutes les attributions `model_has_roles` existantes sont préservées via la clé étrangère sur `role_id`. Aucun utilisateur ne perd son attribution de rôle existante pendant la promotion.
:::

## Accès au panneau et permissions d'écriture fines

Accédez à `/clearance` après l'installation. L'accès en lecture à l'ensemble du panneau nécessite la permission `clearance-access`, vérifiée via `can()`, jamais `hasRole()` :

```php
$user->givePermissionTo('clearance-access');
```

Les opérations d'écriture sur chaque section sont contrôlées par une permission dédiée et délégable - toutes créées par `clearance:install` et assignées à `super_admin` :

| Permission | Contrôle |
|---|---|
| `clearance-permissions-write` | Créer / modifier / supprimer des permissions |
| `clearance-roles-write` | Créer / modifier / supprimer des rôles et leurs attributions de permissions |
| `clearance-guards-write` | Créer / modifier / supprimer des gardes |
| `clearance-settings-write` | Modifier les métadonnées d'affichage, icônes, couleurs, rôle par défaut |
| `clearance-users-write` | Assigner / retirer des rôles pour un utilisateur spécifique |

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

::: tip Accorder un accès restreint
Combinez `clearance-access` avec exactement une permission `clearance-{section}-write` pour confier à un utilisateur (par exemple, un chef d'équipe) l'accès en écriture à une seule section du panneau - les rôles, par exemple - sans exposer les paramètres, les gardes, ou la gestion des utilisateurs.
:::

Si une ligne de permission fine n'existe pas du tout en base de données (un état hérité pré-amorçage), le contrôle se rabat sur `clearance-access` pour la rétrocompatibilité. Sur toute instance ayant exécuté `clearance:install`, ce repli ne s'active jamais.

## Permissions `clearance-*` réservées

Les permissions `clearance-*` sont réservées : elles ne peuvent être créées, renommées, supprimées, ou ajoutées à aucun rôle en dehors du flux d'installation du package lui-même - même par un acteur `super_admin` pour le rôle `super_admin` lui-même.

## Prochaines étapes

- [Permissions](/fr/features/permissions) - le panneau CRUD de permissions général (exclut `clearance-*`).
- [Rôles](/fr/features/roles) - assigner des permissions `clearance-*-write` à un rôle personnalisé.
- [Sécurité](/fr/guide/security) - le modèle complet d'élévation de privilèges et de plafond.
