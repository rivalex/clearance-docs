# Facade

La facade `Clearance` (`Rivalex\Clearance\Facades\Clearance`) espone l'API di autorizzazione contestuale del pacchetto come chiamate in stile statico. È supportata da `src/Clearance.php`, che delega ogni chiamata a [`ContextService`](/it/api/services.md#contextservice).

```php
use Rivalex\Clearance\Facades\Clearance;
```

::: tip
Le direttive Blade e `ContextService` funzionano senza alcuna modifica al tuo modello `User`. La facade è un comodo wrapper attorno allo stesso motore — vedi [Contextual Authorization](/it/features/contextual-authorization.md) per il concetto dietro i controlli limitati al contesto.
:::

## `canIn()`

Verifica se un utente detiene un permesso limitato a un'istanza di contesto.

```php
Clearance::canIn($user, 'orders-create', $store);      // bool
Clearance::canIn($user, 'orders-create', $store, 'api'); // optional 3rd arg: guard name filter
```

| Parametro | Tipo | Descrizione |
|---|---|---|
| `$user` | `Authenticatable` | L'utente da verificare. |
| `string $permission` | `string` | Nome del permesso, es. `orders-create`. |
| `$context` | model instance | Il contesto a cui è limitato il permesso, es. uno `Store`. |
| `?string $guard` | `string\|null` | Filtro opzionale per nome della guardia. |

Restituisce `bool`.

## `hasPermissionIn()`

Alias di [`canIn()`](#canin), nominato per rispecchiare la convenzione `hasPermissionTo()` di Spatie.

```php
Clearance::hasPermissionIn($user, 'orders-create', $store);
```

Restituisce `bool`.

## `hasRoleIn()`

Verifica se un utente detiene un ruolo limitato a un'istanza di contesto.

```php
Clearance::hasRoleIn($user, 'manager', $store);
```

Restituisce `bool`.

## `resolveFor()`

Risolve ogni permesso effettivo che un utente detiene all'interno di un dato contesto.

```php
Clearance::resolveFor($user, $store); // Collection<string>
```

Restituisce una `Collection<string>` di nomi di permessi. Vedi [`ContextService::resolveFor()`](/it/api/services.md#contextservice) per le regole di precedenza a 3 fonti usate per costruire questo elenco.

## `guards()`

Elenca i nomi di tutte le guardie di autenticazione gestite da Clearance.

```php
Clearance::guards(); // Collection<string>
```

Restituisce una `Collection<string>`. Supportata da [`GuardService`](/it/api/services.md#guardservice). Vedi la [pagina della funzionalità Guardie](/it/features/guards.md) per come le guardie sono configurate e iniettate in `auth.guards`.

## Vedi anche

- [Traits & Directives](/it/api/traits-directives.md) — i metodi di istanza equivalenti tramite `HasClearance`, e le direttive Blade sicure per gli ospiti.
- [Services](/it/api/services.md) — il `ContextService` sottostante e i servizi correlati.
