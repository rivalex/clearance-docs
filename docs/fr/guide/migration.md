# Migration depuis une installation Spatie existante

Si votre application utilise déjà `spatie/laravel-permission` avec des rôles, permissions et assignations existants, `clearance:install` les détecte et les préserve - il n'exécute jamais les migrations de Spatie si la table `roles` existe déjà. Voir [Installation](/fr/guide/installation) pour le flux d'installation complet.

Une fois installé, utilisez `clearance:backfill` pour adopter les propres métadonnées de Clearance sur vos rôles et gardes existants.

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

## Ce que fait chaque section

| Section | Effet |
|---|---|
| `meta` | Amorce un nom d'affichage `clr_meta` (nom du rôle en casse de titre) pour chaque rôle qui n'en a pas. |
| `roles` | Amorce les valeurs par défaut `clr_role_meta` (`scope=global`, `is_locked=false`) pour chaque rôle qui n'en a pas. |
| `guards` | Importe les gardes depuis `config('auth.guards')` dans `clr_guards`, filtrés par `allowed_guard_drivers`. |

Utilisez `--only=<section>` pour exécuter une seule section plutôt que les trois.

::: tip Idempotent
Toutes les sections sont idempotentes - relancer `clearance:backfill` est sûr et ne dupliquera ni n'écrasera les lignes `clr_*` existantes.
:::

## Cas particuliers

Le dépôt du package propose un guide de migration plus approfondi couvrant des cas particuliers non résumés ici - notamment ce qui se passe lorsque votre base de données contient déjà un rôle nommé `super_admin` (ou un alias comme `superadmin`, `root`, `owner`) qui entre en collision avec le propre rôle `super_admin` de Clearance, ainsi que des notes sur les sous-classes de modèle `Role`/`Permission` personnalisées et le comportement d'import des gardes. Lisez-le avant d'exécuter un backfill sur une base de données de production avec des données de rôle existantes non triviales. Voir aussi [Super Admin](/fr/features/super-admin) pour le comportement de détection d'alias pendant `clearance:install`.

## Voir aussi

- [Installation](/fr/guide/installation) - le flux complet de `clearance:install`
- [Commandes Artisan](/fr/api/artisan-commands) - référence complète des commandes
- [Architecture](/fr/guide/architecture) - les tables `clr_*` que le backfill peuple
