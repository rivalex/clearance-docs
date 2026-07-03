# Traits & Directives

Référence des traits, concern, contrat, et directives Blade qui composent la surface publique de Clearance en dehors de la facade et des services.

## `HasClearance`

`Rivalex\Clearance\Traits\HasClearance` — à ajouter à votre modèle `User`.

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance;
}
```

Il inclut en interne le `HasRoles` de Spatie, ce trait unique remplace donc les deux — n'ajoutez pas séparément `Spatie\Permission\Traits\HasRoles`.

Il ajoute les méthodes d'instance suivantes :

```php
$user->canIn('orders-create', $store);            // bool
$user->canIn('orders-create', $store, 'api');     // optional guard filter
$user->hasPermissionIn('orders-create', $store);  // alias of canIn()
$user->hasRoleIn('manager', $store);               // bool
$user->hasRoleIn('manager', $store, 'web');         // optional guard filter
```

| Méthode | Renvoie | Description |
|---|---|---|
| `canIn(string $permission, $context, ?string $guard = null)` | `bool` | Vérification de permission limitée à une instance de contexte. |
| `hasPermissionIn(string $permission, $context, ?string $guard = null)` | `bool` | Alias de `canIn()`. |
| `hasRoleIn(string $role, $context, ?string $guard = null)` | `bool` | Vérification de rôle limitée à une instance de contexte. |

::: tip
Sans ce trait, ces méthodes d'instance ne sont pas disponibles — mais les directives Blade et `ContextService` continuent de fonctionner dans tous les cas. Aucun des deux ne dépend du trait.
:::

## `HasPermissionGroups`

`Rivalex\Clearance\Concerns\HasPermissionGroups` — à utiliser lorsque vous avez besoin d'un modèle `Permission` personnalisé (configuré via `config('permission.models.permission')`) au lieu du propre `Rivalex\Clearance\Models\Permission` de Clearance.

Appliquez-le pour conserver le fonctionnement des accesseurs d'interface groupée qui alimentent le panneau Permissions sur votre modèle personnalisé :

```php
use Rivalex\Clearance\Concerns\HasPermissionGroups;

class Permission extends \Spatie\Permission\Models\Permission
{
    use HasPermissionGroups;
}
```

Il fournit les accesseurs suivants :

| Accesseur / méthode | Description |
|---|---|
| `permission_group` | Le préfixe de groupe avant le séparateur de nommage (ex. `orders` dans `orders-create`). |
| `group_string` | Le groupe sous forme de chaîne d'affichage. |
| `abilities()` | L'ensemble des capacités/actions définies pour le groupe de la permission. |
| `colorForAbility()` | Une couleur pour la capacité de la permission, utilisée par l'interface du panneau. |

Voir [Permissions](/fr/features/permissions.md) pour comment la convention de nommage et le regroupement sont utilisés dans le panneau.

## `ClearanceContextable`

`Rivalex\Clearance\Contracts\ClearanceContextable` — à implémenter sur un modèle de contexte (par ex. votre modèle `Store`) pour contrôler comment il est étiqueté dans le panneau.

```php
interface ClearanceContextable
{
    public function clearanceLabel(): string;
}
```

Si un modèle de contexte n'implémente pas ce contrat, le panneau se rabat sur `contextual_models.<FQCN>.label_attribute` (défaut `name`) depuis `config/clearance.php`. Voir [Configuration](/fr/guide/configuration.md).

## Directives Blade

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

| Directive | Vérification équivalente |
|---|---|
| `@canin($permission, $context)` | `ContextService::canIn()` |
| `@hasrolein($role, $context)` | `ContextService::hasRoleIn()` |

::: tip
Les deux directives se résolvent de façon sûre pour un utilisateur invité — elles renvoient `false` / ignorent le bloc et ne lèvent jamais d'exception. Elles ne nécessitent pas non plus le trait `HasClearance` ; elles se résolvent via le même `ContextService` sous-jacent utilisé par la facade.
:::

## Voir aussi

- [Facade](/fr/api/facade.md) — l'équivalent en appel statique de ces méthodes d'instance.
- [Services](/fr/api/services.md) — internals de `ContextService` et la priorité de résolution des 3 sources.
