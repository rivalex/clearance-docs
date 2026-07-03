# Guardie

Il pannello Guardie gestisce le guardie di autenticazione come record di database, così le guardie possono essere aggiunte o modificate senza modificare `config/auth.php` e ridistribuire l'applicazione.

## Cosa fa il pannello

- **Creazione / modifica / eliminazione** delle guardie, ciascuna con un `name`, un `driver`, e un `provider`.

<Screenshot src="/screenshots/guards-page.png" alt="Pannello Guardie di Clearance che elenca le guardie basate su database" caption="Il pannello Guardie elenca ogni guardia basata su database con il suo driver e provider." />

## Come vengono applicate le guardie

Le guardie gestite dal pannello vengono iniettate in `auth.guards` all'avvio dell'applicazione, così sono riconosciute dal sistema di autenticazione di Laravel prima che venga eseguita qualsiasi richiesta - nessuna modifica manuale a `config/auth.php` necessaria.

## Driver allow-listed

Sono accettati solo i driver presenti nell'allow-list:

```php
// config/clearance.php
'allowed_guard_drivers' => ['session', 'token', 'jwt', 'passport', 'sanctum'],
```

::: warning I driver non elencati vengono saltati, non rifiutati rumorosamente
Una guardia con un driver che non è in `allowed_guard_drivers` viene saltata all'avvio (con un avviso nel log) invece di corrompere la configurazione di autenticazione. Se una guardia che hai creato non ha effetto, controlla prima il driver rispetto all'allow-list.
:::

## Controllo degli accessi

L'accesso in lettura richiede `clearance-access`. Creare, modificare, o eliminare una guardia richiede `clearance-guards-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-guards-write');
```

## Route e componente incorporato

| URI | Nome route | Descrizione |
|---|---|---|
| `/clearance/guards` | `clearance.guards` | Gestione guardie (creazione/modifica/eliminazione) |

```blade
<livewire:clearance::guards.guard-manager />
```

## Database

| Tabella | Colonne chiave |
|---|---|
| `clr_guards` | `name` (unico), `driver`, `provider` |

## Prossimi passi

- [Riferimento di configurazione](/it/guide/configuration) - le chiavi di configurazione complete `allowed_guard_drivers` e `guards`.
- [Riferimento Facade](/it/api/facade) - `Clearance::guards()` restituisce i nomi di tutte le guardie di autenticazione gestite.
