# Architecture

## Comment fonctionne la résolution

`resolveFor()` est le cœur de l'autorisation contextuelle de Clearance - voir [Autorisation contextuelle](/fr/features/contextual-authorization) pour l'API complète. Elle fusionne trois sources, dans cet ordre de priorité :

1. **Attributions de rôle contextuelles** - permissions issues des rôles que l'utilisateur détient spécifiquement dans ce contexte (`clr_role_ctx`).
2. **Attributions de rôle globales** - permissions issues des rôles Spatie assignés globalement à l'utilisateur (ainsi `super_admin` et les autres rôles globaux satisfont `canIn()` pour n'importe quel contexte).
3. **Surcharges par contexte** (`clr_ctx_overrides`) - `forced_on` ajoute une permission en plus de ce qui précède ; `forced_off` la retire, même si la permission a été accordée par un rôle global. Un refus l'emporte toujours.

::: warning Un refus l'emporte toujours
`forced_off` prime sur toute permission accordée par un rôle global ou une attribution de rôle contextuelle. Il n'existe aucun moyen de "surclasser" une surcharge `forced_off` pour le même utilisateur/permission/contexte, sinon en la supprimant.
:::

## Base de données

Clearance possède 6 tables et ne modifie jamais les tables principales de Spatie (`roles`, `permissions`, `model_has_roles`, `model_has_permissions`, `role_has_permissions`). Toutes les clés étrangères référençant `roles.id`/`permissions.id` sont en cascade à la suppression sauf mention contraire ; les tables Spatie n'ont aucune clé étrangère pointant vers `clr_*`.

| Table | Colonnes clés |
|---|---|
| `clr_meta` | `subject_type`, `subject_key` (uniques ensemble) - `display_name`, `description`, `icon_svg`, `color` - métadonnées d'affichage pour un rôle ou une garde |
| `clr_role_meta` | `role_id` (unique, FK -> `roles`) - `is_locked`, `scope` (`global`\|`contextual`), `context_types` (json) - `parent_role_id` (FK nullable -> `roles`, `ON DELETE SET NULL`) - rôle plafond |
| `clr_guards` | `name` (unique), `driver`, `provider` - gardes gérés via le panneau |
| `clr_settings` | `key` (unique), `value` - magasin clé/valeur à l'exécution (rôle par défaut, etc.) |
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - attributions de rôle contextuelles, unique par `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) - surcharges de permission par utilisateur, par contexte |

## Ne touche jamais aux tables de Spatie

Clearance est conçu pour coexister avec `spatie/laravel-permission`, pas pour le remplacer. Chaque enregistrement `Role` et `Permission` qu'un panneau Clearance crée ou modifie est un véritable modèle Spatie, stocké dans les propres tables de Spatie. Clearance n'exécute jamais de migration ajoutant une colonne à, ou écrivant une clé étrangère depuis, l'une des tables `roles`, `permissions`, `model_has_roles`, `model_has_permissions`, ou `role_has_permissions`.

Toutes les données propres à Clearance - métadonnées d'affichage, portée et plafonds de rôle, assignations contextuelles, surcharges par contexte, définitions de garde et paramètres - vivent exclusivement dans les 6 tables `clr_*` ci-dessus. Cela signifie que :

- Désinstaller Clearance ne risque jamais de corrompre vos données de rôle/permission Spatie existantes.
- Tout code lisant déjà directement les modèles ou tables de Spatie continue de fonctionner sans modification.
- Les fonctionnalités contextuelles et de plafond de Clearance sont des couches purement additives résolues au moment de la requête (via `resolveFor()` et `RoleService`), et non intégrées au schéma de Spatie.

## Voir aussi

- [Autorisation contextuelle](/fr/features/contextual-authorization) - `canIn()`, `hasRoleIn()`, directives Blade, `ContextService`
- [Rôles](/fr/features/roles) - portée et rôles plafonds, `RoleService::setParent()`
- [Migration](/fr/guide/migration) - amorcer `clr_meta`/`clr_role_meta`/`clr_guards` sur une installation Spatie existante
- [Sécurité](/fr/guide/security) - comment le modèle de plafond et de surcharge prévient l'élévation de privilèges
