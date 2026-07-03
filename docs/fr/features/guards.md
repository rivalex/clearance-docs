# Gardes

Le panneau Gardes gère les gardes d'authentification comme des enregistrements en base de données, afin que les gardes puissent être ajoutés ou modifiés sans éditer `config/auth.php` ni redéployer.

## Ce que fait le panneau

- **Créer / modifier / supprimer** des gardes, chacun avec un `name`, un `driver`, et un `provider`.

<Screenshot src="/screenshots/guards-page.png" alt="Panneau Gardes de Clearance listant les gardes stockés en base de données" caption="Le panneau Gardes liste chaque garde stocké en base de données avec son driver et son fournisseur." />

## Comment les gardes sont appliqués

Les gardes gérés depuis le panneau sont injectés dans `auth.guards` au démarrage de l'application, afin d'être reconnus par le système d'authentification de Laravel avant l'exécution de toute requête - aucune modification manuelle de `config/auth.php` n'est nécessaire.

## Drivers en liste blanche

Seuls les drivers présents dans la liste blanche sont acceptés :

```php
// config/clearance.php
'allowed_guard_drivers' => ['session', 'token', 'jwt', 'passport', 'sanctum'],
```

::: warning Les drivers non listés sont ignorés, pas rejetés bruyamment
Une garde dont le driver n'est pas dans `allowed_guard_drivers` est ignorée au démarrage (avec un avertissement dans les logs) plutôt que de corrompre la configuration d'authentification. Si une garde que vous avez créée ne prend pas effet, vérifiez d'abord son driver par rapport à la liste blanche.
:::

## Contrôle d'accès

L'accès en lecture nécessite `clearance-access`. Créer, modifier, ou supprimer une garde nécessite `clearance-guards-write` :

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-guards-write');
```

## Route et composant intégré

| URI | Nom de route | Description |
|---|---|---|
| `/clearance/guards` | `clearance.guards` | Gestion des gardes (création/modification/suppression) |

```blade
<livewire:clearance::guards.guard-manager />
```

## Base de données

| Table | Colonnes clés |
|---|---|
| `clr_guards` | `name` (unique), `driver`, `provider` |

## Prochaines étapes

- [Référence de configuration](/fr/guide/configuration) - les clés de configuration complètes `allowed_guard_drivers` et `guards`.
- [Référence de la facade](/fr/api/facade) - `Clearance::guards()` renvoie les noms de toutes les gardes d'authentification gérées.
