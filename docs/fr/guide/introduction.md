# Introduction

## Qu'est-ce que Clearance ?

Clearance est un panneau d'administration prêt à l'emploi pour les applications Laravel, construit au-dessus de [spatie/laravel-permission](https://github.com/spatie/laravel-permission). Il ajoute un panneau Livewire 4 + Flux UI pour gérer les permissions, rôles, gardes et paramètres, plus une couche d'autorisation contextuelle et des rôles plafonds - installable en une seule commande artisan, sans altérer aucune table de Spatie.

Clearance vous offre :

- **Un panneau d'administration** pour Permissions, Rôles, Gardes, Paramètres et un Tableau de bord - utilisez les routes prêtes à l'emploi ou intégrez chaque composant Livewire individuellement.
- **Une autorisation contextuelle** - accordez un rôle ou une permission limité à une instance de modèle spécifique (un Store, un Tenant, un Project), avec `$user->canIn()`, les directives Blade `@canin`/`@hasrolein`, et un `ContextService` pour une utilisation en dehors du modèle User.
- **Des rôles plafonds** - un rôle peut déclarer un rôle parent dont l'ensemble de permissions agit comme une limite supérieure ; un rôle enfant ne peut jamais dépasser son parent, contrôle appliqué automatiquement à chaque enregistrement.
- **Des permissions d'écriture fines** - une permission `clearance-{section}-write` délégable par section du panneau, afin de pouvoir confier "gérer les rôles" sans confier également "gérer les paramètres."
- **Un Super Admin** avec un contournement de Gate global optionnel, une détection d'alias pour les rôles administrateur existants, et une promotion sûre et non destructive.
- **Un module Utilisateurs** (optionnel) - assignez des rôles globalement ou à un contexte spécifique, plus des surcharges de permissions par contexte, depuis un panneau par utilisateur.
- **Un panneau Paramètres** - nom d'affichage, description, icône SVG assainie et couleur par rôle/garde ; un rôle par défaut configurable avec attribution automatique et attribution en masse.
- **Une gestion des Gardes** - gardes stockés en base de données, injectés dans `auth.guards` au démarrage, restreints à un ensemble de drivers en liste blanche.
- **9 langues incluses** (ar, en, es, fr, hi, it, pt, ru, zh), parité des clés testée.
- **Des points d'extension** - le trait `HasClearance`, le trait `HasPermissionGroups`, le contrat `ClearanceContextable`, des composants Blade réutilisables.
- **Un assainissement SVG** - chaque icône fournie par l'utilisateur passe par un assainisseur en liste blanche avant le stockage et au moment du rendu.
- **Une sécurité renforcée** - plafonds anti-élévation de privilèges sur chaque chemin d'attribution de permission, surcharges de contexte à autorité de refus, espace de noms `clearance-*` protégé. Voir [Sécurité](/fr/guide/security).

## Relation avec spatie/laravel-permission

Clearance ne remplace pas le package de permissions de Spatie - c'est une couche d'interface et d'autorisation construite dessus. Les rôles et permissions restent les modèles `Role` et `Permission` de Spatie, toujours stockés dans les tables `roles`, `permissions`, `model_has_roles`, `model_has_permissions` et `role_has_permissions` de Spatie. Clearance ne modifie jamais ces tables. Tout ce que Clearance ajoute - métadonnées d'affichage, portée des rôles, plafonds, assignations contextuelles, surcharges par contexte - vit dans son propre ensemble de tables `clr_*`. Voir [Architecture](/fr/guide/architecture) pour le détail complet.

## Prérequis

| Dépendance | Version |
|---|---|
| PHP | ^8.3 |
| Laravel | ^11.0 \| ^12.0 \| ^13.0 |
| spatie/laravel-permission | ^6.0 |
| livewire/livewire | ^3.0 \| ^4.0 |
| livewire/flux | ^2.14 |

## Concepts clés

| Concept | Résumé |
|---|---|
| Permission | Une permission Spatie suivant la convention de nommage `group-action` (ex. `orders-create`). Accorde une capacité. |
| Portée du rôle | Chaque rôle est `global` (assigné directement, s'applique partout) ou `contextual` (lié à des types de modèle spécifiques, assignable uniquement au sein d'une instance de contexte). |
| Rôle plafond | Un rôle peut déclarer un rôle parent dont l'ensemble de permissions constitue une limite supérieure - l'enfant ne peut jamais la dépasser. |
| Contexte | Une instance de modèle (un Store, un Tenant, un Project) à laquelle un rôle ou une surcharge de permission peut être limité. |
| Surcharge | Une attribution ou un refus de permission `forced_on`/`forced_off` par utilisateur, par contexte - un refus l'emporte toujours. |

Chacun de ces concepts dispose de sa propre page complète : [Permissions](/fr/features/permissions), [Rôles](/fr/features/roles), [Autorisation contextuelle](/fr/features/contextual-authorization), [Utilisateurs](/fr/features/users), [Super Admin](/fr/features/super-admin).

## Prochaines étapes

Prêt à installer Clearance ? Continuez avec le [guide d'installation](/fr/guide/installation).
