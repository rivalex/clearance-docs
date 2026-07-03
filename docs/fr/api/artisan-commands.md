# Commandes Artisan

Clearance fournit deux commandes Artisan : `clearance:install` pour la configuration initiale, et `clearance:backfill` pour amorcer les valeurs par défaut sur les installations existantes. Voir le [guide d'installation](/fr/guide/installation.md) pour le parcours de configuration de bout en bout.

## `clearance:install`

```bash
php artisan clearance:install
php artisan clearance:install --user=1
php artisan clearance:install --role=editor
php artisan clearance:install --super-admin-role=owner
php artisan clearance:install --force
```

| Option | Description |
|---|---|
| `--user=ID` | Assigne le rôle `super_admin` à un utilisateur après l'installation. |
| `--role=NAME` | Assigne `clearance-access` à un rôle (créé s'il est absent). |
| `--super-admin-role=NAME` | Promeut un rôle existant en `super_admin` (évite l'invite interactive). |
| `--force` | Relance même si déjà installé. |

### Ce qu'elle fait, dans l'ordre

1. Publie `config/clearance.php`.
2. Publie les migrations de Clearance.
3. Détecte les tables Spatie Permission — si la table `roles` est absente, publie et exécute d'abord automatiquement les migrations de Spatie.
4. Exécute toutes les migrations en attente.
5. Crée `clearance-access` ainsi que les 5 permissions de capacité fines : `clearance-permissions-write`, `clearance-roles-write`, `clearance-guards-write`, `clearance-settings-write`, `clearance-users-write`.
6. Crée le rôle `super_admin` avec toutes les permissions `clearance-*` assignées. Cette étape est additive — elle ne retire jamais de permissions existantes lors d'une relance.
7. Écrit `storage/.clearance-installed` comme marqueur d'idempotence.

### Détection d'alias

Si la base de données contient déjà un rôle nommé `super_admin`, `superadmin`, `super-admin`, `root`, ou `owner`, l'installateur le détecte et vous invite de façon interactive à le promouvoir ou à créer un rôle `super_admin` séparé. Utilisez `--super-admin-role=NAME` pour éviter l'invite de façon non interactive.

La promotion renomme le rôle en place — toutes les attributions `model_has_roles` sont préservées via la clé étrangère sur `role_id`.

::: warning
Les permissions `clearance-*` sont protégées en dehors du flux d'installation — voir [`PermissionService`](/fr/api/services.md#permissionservice) et `ClearanceProtectedResourceException` dans [Services](/fr/api/services.md#exceptions).
:::

## `clearance:backfill`

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

| Option | Description |
|---|---|
| `--only=meta\|roles\|guards` | Exécute une seule section de backfill. |
| `--dry-run` | Prévisualise les changements sans écrire. |

### Sections

| Section | Description |
|---|---|
| `meta` | Amorce un nom d'affichage `clr_meta` (nom du rôle en casse de titre) pour chaque rôle qui n'en a pas. |
| `roles` | Amorce les valeurs par défaut `clr_role_meta` (`scope=global`, `is_locked=false`) pour chaque rôle qui n'en a pas. |
| `guards` | Importe les gardes depuis `config('auth.guards')` dans `clr_guards`, filtrés par `allowed_guard_drivers`. |

Toutes les sections sont idempotentes — exécuter `clearance:backfill` de façon répétée est sûr et ne fait que compléter ce qui manque.

::: tip
Pour les installations Spatie préexistantes : `clearance:install` n'exécute jamais les migrations de Spatie si la table `roles` existe déjà, de sorte que les rôles, permissions, et attributions existants sont préservés automatiquement.
:::

## Voir aussi

- [Services](/fr/api/services.md) — `GuardService` et `MetaService`, qui alimentent les sections de backfill `guards` et `meta`.
- [Guide de migration](/fr/guide/migration.md) — migrer une installation Spatie Permission existante vers Clearance.
