# Configuration

Clearance est fourni avec des valeurs par défaut sensées, il fonctionne donc dès la sortie de la boîte après l'[installation](/fr/guide/installation). Publiez le fichier de configuration lorsque vous devez personnaliser le préfixe de route, le middleware, les modèles contextuels, ou tout autre paramètre.

```bash
php artisan vendor:publish --tag=clearance-config
```

Cela publie `config/clearance.php`.

## Référence de configuration

Voici la référence définitive de chaque clé de `config/clearance.php`. Les autres pages de cette documentation renvoient ici plutôt que de répéter le tableau.

| Clé | Défaut | Rôle |
|---|---|---|
| `route_prefix` | `'clearance'` | Préfixe d'URI pour toutes les routes du panneau. |
| `middleware` | `['web', 'auth']` | Appliqué à toutes les routes du panneau avant `clearance.access`. |
| `access_permission` | `'clearance-access'` | Permission vérifiée par le middleware d'accès. |
| `user_model` | `null` | Par défaut, utilise `auth.providers.users.model`. |
| `modules.users` | `false` | Interrupteur principal pour le module Utilisateurs (routes + composants Livewire). |
| `contextual_models` | `[]` | `FQCN => ['label', 'icon', 'label_attribute', 'searchable']` - modèles pouvant servir de contextes de liaison de rôle. |
| `enforce_naming_convention` | `true` | Impose la convention de nommage `group-action` des permissions. |
| `naming_separator` | `'-'` | Caractère séparateur - `'-'` ou `'_'` uniquement. |
| `guards` | `[]` | Remplace les gardes auto-détectés depuis `config/auth.php`. Vide = auto-détection de tous. |
| `allowed_guard_drivers` | `['session', 'token', 'jwt', 'passport', 'sanctum']` | Drivers acceptés lors de l'injection des gardes en base de données dans `auth.guards`. |
| `layout` | `null` | Layout Blade pour les composants pleine page. `null` = layout par défaut de l'application hôte (`config('livewire.layout')`). |
| `auto_assign_default_role` | `false` | Assigne automatiquement le rôle par défaut configuré dans les Paramètres lors de l'événement `Registered`. |
| `super_admin_gate_bypass` | `false` | Optionnel : `super_admin` contourne toutes les vérifications de Gate à l'échelle de l'application. |
| `ui.flux_pro` | `null` | `null` = auto-détection via `Flux::pro()`. |

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

Désactivez le contrôle par environnement avec `enforce_naming_convention` :

```php
'enforce_naming_convention' => false,
```

::: warning Le séparateur est validé au démarrage
La valeur `naming_separator` est le seul élément de cette configuration qui atteint un fragment SQL brut (regroupement/tri des permissions), c'est pourquoi seuls `-` ou `_` sont acceptés.
:::

## Personnaliser le middleware

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

Les noms de route restent stables quels que soient les changements de préfixe ou de middleware - voir le [Démarrage rapide](/fr/guide/quick-start) pour le tableau complet des routes.

## Voir aussi

- [Démarrage rapide](/fr/guide/quick-start) - monter le panneau avec ces paramètres appliqués
- [Publication des assets](/fr/guide/publishing) - publier les traductions et comprendre la clé `layout`
- [Gardes](/fr/features/guards) - comment `guards` et `allowed_guard_drivers` sont utilisés
- [Super Admin](/fr/features/super-admin) - comment fonctionne `super_admin_gate_bypass`
