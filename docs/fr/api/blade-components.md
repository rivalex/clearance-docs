# Composants Blade

Clearance enregistre un petit ensemble de composants Blade sous l'espace de noms `clearance::`. Tous sont directement réutilisables dans les vues de votre propre application, pas seulement à l'intérieur du panneau.

## Composants

### `x-clearance::message`

Affichage de message flash/statut.

```blade
<x-clearance::message>
    Changes saved successfully.
</x-clearance::message>
```

### `x-clearance::clipboard`

Bouton/wrapper de copie dans le presse-papiers — utilisé, par exemple, pour afficher des noms de route ou des identifiants avec une action de copie en un clic.

```blade
<x-clearance::clipboard :value="$route->name" />
```

### `x-clearance::validation-errors`

Rendu de la liste d'erreurs de validation standard de Laravel.

```blade
<x-clearance::validation-errors />
```

### `x-clearance::icon.*`

Un petit ensemble d'icônes intégré, utilisé dans tout le panneau. Aucune dépendance JS externe au-delà d'Alpine, déjà requis par Livewire.

```blade
<x-clearance::icon.check />
```

## Composants Livewire

Pour référence, voici les noms de composants Livewire pleine page/intégrables enregistrés par le package, utilisables avec l'intégration `<livewire:clearance::...>` dans vos propres vues :

| Nom du composant | Description |
|---|---|
| `dashboard` | Le tableau de bord du panneau. |
| `permissions.permission-manager` | Panneau CRUD des Permissions. |
| `roles.role-manager` | Panneau CRUD des Rôles, y compris les rôles plafonds. |
| `guards.guard-manager` | Panneau CRUD des Gardes. |
| `settings.settings-manager` | Panneau Paramètres (métadonnées d'affichage, icônes, couleurs). |
| `users.user-clearance-manager` | Panneau d'attribution de rôle/permission par utilisateur. Prend `:userId`. |

```blade
<livewire:clearance::dashboard />

<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

Voir les pages de fonctionnalités correspondantes pour chaque panneau : [Tableau de bord](/fr/features/dashboard.md), [Permissions](/fr/features/permissions.md), [Rôles](/fr/features/roles.md), [Gardes](/fr/features/guards.md), [Paramètres](/fr/features/settings.md), [Utilisateurs](/fr/features/users.md).

::: tip
La clé `config('clearance.ui.flux_pro')` contrôle si les éléments Flux UI réservés à Pro s'affichent dans ces composants. `null` (par défaut) auto-détecte Flux Pro via `Flux::pro()`.
:::
