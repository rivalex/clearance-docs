# Démarrage rapide

Après l'[installation](/fr/guide/installation), vous disposez de deux façons d'exposer le panneau Clearance : les routes prêtes à l'emploi, ou l'intégration de composants Livewire individuels n'importe où dans votre application.

## Option A - Routes prêtes à l'emploi

Après l'installation, le panneau est disponible à `/clearance` (ou le `route_prefix` configuré, voir [Configuration](/fr/guide/configuration)). Toutes les routes sont protégées par le middleware `clearance.access` et utilisent automatiquement le layout Livewire de l'application hôte.

| URI | Nom de route | Description |
|---|---|---|
| `/clearance` | `clearance.home` | Tableau de bord |
| `/clearance/guards` | `clearance.guards` | Gestion des gardes (création/modification/suppression) |
| `/clearance/permissions` | `clearance.permissions` | CRUD des permissions avec validation du nommage par préfixe de groupe |
| `/clearance/roles` | `clearance.roles` | CRUD des rôles avec attribution de permissions limitée à la garde, portée (global/contextuel), et plafond |
| `/clearance/settings` | `clearance.settings` | Métadonnées d'affichage (icônes, couleurs), rôle par défaut, paramètres généraux |
| `/clearance/user/{userId}` | `clearance.user` | Attribution de rôle par utilisateur + surcharges de permissions contextuelles (nécessite `modules.users = true`) |

Les noms de route restent stables quels que soient les changements de préfixe. Pour personnaliser le middleware :

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

## Option B - Composants Livewire intégrés

Chaque section du panneau peut être montée individuellement dans n'importe quelle vue Blade, page Filament, ou route personnalisée :

```blade
<livewire:clearance::dashboard />
<livewire:clearance::permissions.permission-manager />
<livewire:clearance::roles.role-manager />
<livewire:clearance::guards.guard-manager />
<livewire:clearance::settings.settings-manager />
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

## Où aller ensuite

- [Référence de configuration](/fr/guide/configuration) - chaque clé de configuration, ses défauts et son rôle
- [Autorisation contextuelle](/fr/features/contextual-authorization) - limiter des rôles et permissions à un Store, Tenant, ou Project
- [Rôles](/fr/features/roles) - portée et rôles plafonds
- [Tableau de bord](/fr/features/dashboard) - ce que montre le composant Tableau de bord
- [Utilisateurs](/fr/features/users) - le panneau optionnel par utilisateur
