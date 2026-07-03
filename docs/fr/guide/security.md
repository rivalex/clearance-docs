# Sécurité

Clearance est passé par un audit de sécurité dédié avant publication, suivi de deux passes de renforcement supplémentaires avant la sortie. Points forts : plafonds anti-élévation de privilèges sur chaque chemin d'attribution de permission (attributions directes comme synchronisation au niveau du rôle), surcharges de contexte à autorité de refus, un assainisseur SVG en liste blanche, un espace de noms de permission `clearance-*` protégé, et des directives Blade sûres pour les invités.

## Accès au panneau et permissions d'écriture fines

L'accès en lecture à l'ensemble du panneau nécessite la permission `clearance-access` (vérifiée via `can()`, jamais `hasRole()`). Les opérations d'écriture sur chaque section sont contrôlées par une permission dédiée et délégable - toutes créées par `clearance:install` et assignées à `super_admin` :

| Permission | Contrôle |
|---|---|
| `clearance-permissions-write` | Créer / modifier / supprimer des permissions |
| `clearance-roles-write` | Créer / modifier / supprimer des rôles et leurs attributions de permissions |
| `clearance-guards-write` | Créer / modifier / supprimer des gardes |
| `clearance-settings-write` | Modifier les métadonnées d'affichage, icônes, couleurs, rôle par défaut |
| `clearance-users-write` | Assigner / retirer des rôles pour un utilisateur spécifique |

Si une ligne de permission fine n'existe pas du tout en base de données (un état hérité pré-amorçage), le contrôle se rabat sur `clearance-access` pour la rétrocompatibilité.

## Espace de noms `clearance-*` protégé

Les permissions `clearance-*` sont réservées : elles ne peuvent être créées, renommées, supprimées, ou ajoutées à aucun rôle en dehors du flux d'installation du package lui-même - même par un acteur `super_admin` pour le rôle `super_admin` lui-même.

## Plafonds anti-élévation de privilèges

Chaque chemin d'attribution de permission dans Clearance applique un plafond afin qu'un acteur ne puisse jamais accorder plus qu'il ne détient lui-même :

- **Rôles plafonds** - un rôle peut déclarer un rôle parent dont l'ensemble de permissions agit comme une limite supérieure. Toute permission excédentaire est silencieusement retirée lors de `RoleService::setParent()` et à chaque synchronisation de permission ultérieure - aucune exception, aucun message d'erreur. Retirer des permissions d'un parent propage automatiquement la suppression à chaque enfant. Voir [Rôles](/fr/features/roles) pour l'API complète du plafond.
- **Module Utilisateurs** - l'attribution de rôle comme les attributions de permission directes/par surcharge appliquent toutes deux un plafond de privilège : un acteur non-`super_admin` ne peut pas modifier ses propres attributions, et ne peut pas accorder une permission qu'il ne détient pas lui-même. Voir [Utilisateurs](/fr/features/users).

## Surcharges de contexte à autorité de refus

Les surcharges de permission par contexte (`clr_ctx_overrides`) prennent en charge `forced_on` et `forced_off`. `forced_off` l'emporte toujours - même sur une permission accordée par un rôle global. Il n'existe aucun chemin permettant à une attribution de priorité inférieure de surcharger un refus explicite. Voir [Architecture](/fr/guide/architecture#how-resolution-works) pour l'ordre de résolution complet.

## Assainissement SVG

Chaque icône fournie par l'utilisateur (métadonnées d'affichage de rôle/garde dans le panneau Paramètres) passe par un assainisseur en liste blanche à la fois avant le stockage et au moment du rendu - pas une simple vérification unique à l'envoi.

## Directives Blade sûres pour les invités

`@canin` et `@hasrolein` se résolvent tous deux de façon sûre (renvoient `false`) pour un utilisateur invité - elles ne lèvent jamais d'exception.

## Le contournement de Gate est à l'échelle de l'application

`super_admin_gate_bypass` (défaut `false`) contrôle si le rôle `super_admin` contourne toutes les vérifications Laravel `can()` / `Gate::allows()` / policy à l'échelle globale, via un hook `Gate::before()`.

::: warning Non limité au panneau
Une fois activé, tout utilisateur détenant `super_admin` contourne toutes les vérifications de Gate à l'échelle de l'application - pas seulement les vérifications du panneau Clearance. Activez-le délibérément. Voir [Super Admin](/fr/features/super-admin) pour la détection d'alias et le comportement de promotion.
:::

## Points d'attention liés à la sécurité

- `forced_off` l'emporte toujours - même sur une permission accordée par un rôle global.
- Un rôle plafond ne fait que retirer, jamais accorder - définir un parent permissif n'accorde rien à l'enfant par lui-même.
- Le contournement de Gate (`super_admin_gate_bypass`) s'applique à toute l'application, pas uniquement au panneau.
- Le module Utilisateurs est désactivé par défaut (`modules.users = false`).
- Les permissions `clearance-*` sont intouchables en dehors du flux d'installation, même pour `super_admin`.
- Exécutez toujours `clearance:install`, jamais un simple `migrate`, sur une installation neuve - voir [Installation](/fr/guide/installation).

## Voir aussi

- [Architecture](/fr/guide/architecture) - ordre de priorité de résolution et schéma `clr_*`
- [Rôles](/fr/features/roles) - les rôles plafonds en détail
- [Utilisateurs](/fr/features/users) - le plafond de privilège sur l'attribution de rôle/permission
- [Super Admin](/fr/features/super-admin) - contournement de Gate et détection d'alias
