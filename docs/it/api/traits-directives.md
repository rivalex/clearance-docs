# Trait e Direttive

Riferimento per i trait, il concern, il contratto, e le direttive Blade che compongono la superficie rivolta agli sviluppatori di Clearance al di fuori della facade e dei servizi.

## `HasClearance`

`Rivalex\Clearance\Traits\HasClearance` — aggiungilo al tuo modello `User`.

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance;
}
```

Include internamente `HasRoles` di Spatie, quindi questo unico trait sostituisce entrambi — non aggiungere `Spatie\Permission\Traits\HasRoles` separatamente.

Aggiunge i seguenti metodi di istanza:

```php
$user->canIn('orders-create', $store);            // bool
$user->canIn('orders-create', $store, 'api');     // optional guard filter
$user->hasPermissionIn('orders-create', $store);  // alias of canIn()
$user->hasRoleIn('manager', $store);               // bool
$user->hasRoleIn('manager', $store, 'web');         // optional guard filter
```

| Metodo | Restituisce | Descrizione |
|---|---|---|
| `canIn(string $permission, $context, ?string $guard = null)` | `bool` | Controllo di permesso limitato a un'istanza di contesto. |
| `hasPermissionIn(string $permission, $context, ?string $guard = null)` | `bool` | Alias di `canIn()`. |
| `hasRoleIn(string $role, $context, ?string $guard = null)` | `bool` | Controllo di ruolo limitato a un'istanza di contesto. |

::: tip
Senza questo trait, questi metodi di istanza non sono disponibili — ma le direttive Blade e `ContextService` continuano a funzionare comunque. Nessuno dei due dipende dal trait.
:::

## `HasPermissionGroups`

`Rivalex\Clearance\Concerns\HasPermissionGroups` — usalo quando hai bisogno di un modello `Permission` personalizzato (configurato tramite `config('permission.models.permission')`) invece del modello `Rivalex\Clearance\Models\Permission` proprio di Clearance.

Applicalo per mantenere funzionanti sul tuo modello personalizzato gli accessor raggruppati per la UI che alimentano il pannello Permessi:

```php
use Rivalex\Clearance\Concerns\HasPermissionGroups;

class Permission extends \Spatie\Permission\Models\Permission
{
    use HasPermissionGroups;
}
```

Fornisce questi accessor:

| Accessor / metodo | Descrizione |
|---|---|
| `permission_group` | Il prefisso di gruppo prima del separatore di naming (es. `orders` in `orders-create`). |
| `group_string` | Il gruppo come stringa di visualizzazione. |
| `abilities()` | L'insieme di abilità/azioni definite per il gruppo del permesso. |
| `colorForAbility()` | Un colore per l'abilità del permesso, usato dalla UI del pannello. |

Vedi [Permissions](/it/features/permissions.md) per come vengono usate la convenzione di naming e il raggruppamento nel pannello.

## `ClearanceContextable`

`Rivalex\Clearance\Contracts\ClearanceContextable` — implementalo su un modello di contesto (es. il tuo modello `Store`) per controllare come viene etichettato nel pannello.

```php
interface ClearanceContextable
{
    public function clearanceLabel(): string;
}
```

Se un modello di contesto non implementa questo contratto, il pannello ricade su `contextual_models.<FQCN>.label_attribute` (predefinito `name`) da `config/clearance.php`. Vedi [Configuration](/it/guide/configuration.md).

## Direttive Blade

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

| Direttiva | Controllo equivalente |
|---|---|
| `@canin($permission, $context)` | `ContextService::canIn()` |
| `@hasrolein($role, $context)` | `ContextService::hasRoleIn()` |

::: tip
Entrambe le direttive si risolvono in modo sicuro per un utente ospite — restituiscono `false` / saltano il blocco e non lanciano mai un'eccezione. Inoltre non richiedono il trait `HasClearance`; si risolvono tramite lo stesso `ContextService` sottostante usato dalla facade.
:::

## Vedi anche

- [Facade](/it/api/facade.md) — l'equivalente in chiamate statiche di questi metodi di istanza.
- [Services](/it/api/services.md) — gli interni di `ContextService` e la precedenza di risoluzione a 3 fonti.
