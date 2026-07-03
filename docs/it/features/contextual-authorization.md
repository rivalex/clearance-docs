# Autorizzazione Contestuale

Clearance estende il modello di autorizzazione globale di Spatie con controlli limitati al contesto. I ruoli di un utente possono essere assegnati all'interno di uno specifico contesto (es. uno Store, un Tenant, o un Project) e sono invisibili al di fuori di quell'ambito. Questo è il meccanismo centrale dietro il [modulo Users](/it/features/users) e i [Roles](/it/features/roles#scope-global-vs-contextual) contestuali.

## Sul modello User

Disponibile quando il tuo modello `User` usa il trait `HasClearance` (vedi [Installation](/it/guide/installation#add-hasclearance-to-your-user-model)):

```php
// Does the user have this permission in this context?
$user->canIn('orders-create', $store);

// Alias - mirrors Spatie's hasPermissionTo() naming
$user->hasPermissionIn('orders-create', $store);

// Does the user hold this role in this context?
$user->hasRoleIn('manager', $store);

// Optionally filter by guard
$user->canIn('orders-create', $store, 'api');
$user->hasRoleIn('manager', $store, 'web');
```

## Nelle viste Blade

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

::: tip Sicuro per gli ospiti per progettazione
Entrambe le direttive si risolvono in modo sicuro (restituiscono `false`) per un utente ospite - non lanciano mai un'eccezione. Puoi usare `@canin`/`@hasrolein` in viste renderizzate prima che l'autenticazione sia garantita, senza racchiuderle in un controllo `@auth`.
:::

## Tramite ContextService

Da usare in controller, policy, o job dove il modello `User` potrebbe non avere il trait `HasClearance`:

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

## Tramite la facade Clearance

La facade `Clearance` delega allo stesso servizio - un comodo punto di ingresso globale:

```php
Clearance::canIn($user, 'orders-create', $store);
Clearance::resolveFor($user, $store);
Clearance::guards(); // names of all managed authentication guards
```

Vedi il [riferimento Facade](/it/api/facade) per l'elenco completo dei metodi.

## Come funziona la risoluzione

`resolveFor()` unisce tre fonti, in questo ordine di precedenza:

1. **Concessioni di ruolo contestuali** - permessi dai ruoli che l'utente detiene specificamente in questo contesto (`clr_role_ctx`).
2. **Concessioni di ruolo globali** - permessi dai ruoli Spatie assegnati globalmente all'utente (così `super_admin` e altri ruoli globali soddisfano `canIn()` per qualsiasi contesto).
3. **Override per contesto** (`clr_ctx_overrides`) - `forced_on` aggiunge un permesso oltre a quanto sopra; `forced_off` lo rimuove, anche se il permesso era stato concesso da un ruolo globale.

::: danger Un diniego vince sempre
Se un permesso è `forced_off` per un utente in un dato contesto, quel diniego prevale su ogni altra fonte - inclusa una concessione di ruolo globale e una concessione di ruolo contestuale. Non esiste modo di riconcedere un permesso `forced_off` per quel contesto se non rimuovendo l'override stesso. Progetta la tua policy di override tenendo conto di questo: `forced_off` è il segnale più forte nel sistema.
:::

## Database

| Tabella | Colonne chiave |
|---|---|
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - unico per `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) |

## Prossimi passi

- [Users](/it/features/users) - l'interfaccia del pannello per assegnare ruoli e override contestuali.
- [Roles](/it/features/roles#scope-global-vs-contextual) - dichiarare un ruolo come `contextual` e i suoi `context_types` ammessi.
- [Riferimento Traits & Directives](/it/api/traits-directives) - il trait `HasClearance` completo e le firme delle direttive Blade.
- [Riferimento Services](/it/api/services) - API completa di `ContextService`.
