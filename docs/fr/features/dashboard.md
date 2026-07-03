# Tableau de bord

Le Tableau de bord est la page d'accueil du panneau Clearance. Il fournit un résumé en un coup d'œil de l'état d'autorisation de votre application - aucune configuration requise.

## Ce qu'il affiche

- **Total des rôles** - nombre total de rôles Spatie.
- **Total des gardes** - nombre total de gardes gérés par Clearance (voir [Gardes](/fr/features/guards)).
- **Total des permissions** - nombre total de permissions Spatie.
- **Groupes de permissions** - le nombre de groupes de permissions distincts, dérivé du préfixe de nommage (le `group` dans `group-action`, ex. `orders` dans `orders-create`). Voir [Permissions](/fr/features/permissions) pour la convention de nommage.
- **Assignations de rôle contextuelles** - le nombre d'attributions de rôle limitées à une instance de modèle spécifique (un Store, un Tenant, un Project). Voir [Autorisation contextuelle](/fr/features/contextual-authorization).
- **Top 5 des rôles par nombre d'utilisateurs** - les cinq rôles ayant le plus d'utilisateurs assignés, classés.

<Screenshot src="/screenshots/dashboard-page.png" alt="Tableau de bord Clearance affichant le nombre de rôles, gardes et permissions" caption="Le Tableau de bord résume les rôles, gardes, permissions, groupes, assignations contextuelles, et les rôles principaux." />

## Accéder au Tableau de bord

| URI | Nom de route | Description |
|---|---|---|
| `/clearance` | `clearance.home` | Tableau de bord |

La lecture du Tableau de bord nécessite la permission `clearance-access`, comme toute autre section du panneau. Voir [Super Admin](/fr/features/super-admin#panel-access-fine-grained-write-permissions) pour le tableau complet de contrôle d'accès.

## Intégrer le composant

Le Tableau de bord est un composant Livewire autonome. Montez-le n'importe où dans vos propres layouts, à la place de - ou en complément de - la route prête à l'emploi `/clearance` :

```blade
<livewire:clearance::dashboard />
```

## Prochaines étapes

- [Permissions](/fr/features/permissions) - gérer la liste de permissions alimentant les compteurs du Tableau de bord.
- [Rôles](/fr/features/roles) - gérer les rôles derrière la statistique "top 5 par nombre d'utilisateurs".
- [Autorisation contextuelle](/fr/features/contextual-authorization) - ce qu'est une assignation de rôle contextuelle.
