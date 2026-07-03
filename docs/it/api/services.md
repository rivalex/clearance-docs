# Servizi

Tutti i servizi di Clearance vivono in `src/Services/` e vengono risolti dal container, es. `app(ContextService::class)`. Sono il livello a cui rivolgersi da controller, policy, e job — ovunque il modello `User` potrebbe non usare il trait `HasClearance`.

## `ContextService`

`Rivalex\Clearance\Services\ContextService` (`src/Services/ContextService.php`) è il motore dietro l'autorizzazione contestuale. Sia la [facade `Clearance`](/it/api/facade.md) sia il trait `HasClearance` delegano ad esso.

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

`resolveFor()` costruisce il suo risultato usando una precedenza a 3 fonti:

1. Concessioni di ruolo contestuali
2. Concessioni di ruolo globali
3. Override per contesto (`forced_on` aggiunge, `forced_off` vince sempre)

Vedi [Contextual Authorization](/it/features/contextual-authorization.md) per la spiegazione concettuale di questa precedenza.

## `RoleService`

`Rivalex\Clearance\Services\RoleService` (`src/Services/RoleService.php`) gestisce i ruoli ceiling.

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
app(RoleService::class)->removeParent($childRole);
```

| Metodo | Descrizione |
|---|---|
| `setParent($childRole, $parentRole)` | Imposta un ceiling: i permessi in eccesso su `$childRole` oltre il set del genitore vengono rimossi silenziosamente. Lancia `ClearanceScopeViolationException` se il figlio è già un genitore, o il genitore è già un figlio. |
| `removeParent($childRole)` | Rimuove la relazione di ceiling. |

::: warning
Un ceiling rimuove soltanto — non concede mai. Un ruolo genitore permissivo non dà di per sé alcun permesso al figlio. La rimozione di un permesso da un ruolo genitore propaga automaticamente quella rimozione a ogni ruolo figlio.
:::

Vedi [Roles](/it/features/roles.md) per il concetto di ruolo ceiling più in dettaglio.

## `PermissionService`

`Rivalex\Clearance\Services\PermissionService` (`src/Services/PermissionService.php`) supporta il CRUD del pannello Permessi.

Applica la convenzione di naming `group-action`, controllata dalle chiavi di configurazione `enforce_naming_convention` e `naming_separator`, e protegge il namespace di permessi `clearance-*` dall'essere creato, rinominato, o eliminato al di fuori del flusso di installazione.

## `GuardService`

`Rivalex\Clearance\Services\GuardService` (`src/Services/GuardService.php`) supporta il CRUD del pannello Guardie.

Inietta le guardie basate su database in `auth.guards` all'avvio, filtrate dalla chiave di configurazione `allowed_guard_drivers` — un driver non elencato viene saltato con un avviso nel log invece di corrompere la configurazione di autenticazione.

## `MetaService`

`Rivalex\Clearance\Services\MetaService` (`src/Services/MetaService.php`) legge e scrive `clr_meta` (nome visualizzato, descrizione, icona SVG sanitizzata, colore) per un soggetto ruolo o guardia. Supporta l'editor dei metadati di visualizzazione del pannello Impostazioni.

Le icone SVG passano attraverso `Rivalex\Clearance\Support\SvgSanitizer` (un sanitizzatore allow-list) sia prima della memorizzazione sia in fase di rendering.

## `UserClearanceService`

`Rivalex\Clearance\Services\UserClearanceService` (`src/Services/UserClearanceService.php`) supporta il pannello del modulo Utenti:

- Assegnazione di ruolo globale
- Assegnazione di ruolo contestuale (tramite `UserRoleContext`)
- Override dei permessi per contesto (tramite `UserContextPermissionOverride`, `forced_on` / `forced_off`)
- Rimozione delle assegnazioni, che propaga l'eliminazione degli override

Applica un ceiling di privilegio: un attore non `super_admin` non può modificare le proprie assegnazioni, e non può concedere un permesso che non detiene lui stesso. Vedi [Super Admin](/it/features/super-admin.md) e [Users](/it/features/users.md).

## Eccezioni

Tutte in `src/Exceptions/`:

| Eccezione | Lanciata quando |
|---|---|
| `ClearanceConfigException` | Viene letto un valore non valido o mal configurato di `config/clearance.php` (es. un `naming_separator` non valido). |
| `ClearanceNamingException` | Un nome di permesso viola la convenzione di naming `group-action` mentre l'applicazione è attiva. |
| `ClearanceProtectedResourceException` | Viene tentata una modifica a un permesso `clearance-*` protetto, o a una risorsa bloccata, al di fuori del flusso di installazione. |
| `ClearanceScopeViolationException` | Una relazione di ruolo ceiling non valida (un genitore che cerca di diventare figlio, o viceversa), o un disallineamento di ambito. |

## Vedi anche

- [Facade](/it/api/facade.md) — il wrapper in chiamate statiche attorno a `ContextService`.
- [Traits & Directives](/it/api/traits-directives.md) — gli equivalenti in metodi di istanza e direttive Blade.
- [Artisan Commands](/it/api/artisan-commands.md) — `clearance:install` e `clearance:backfill`, che generano i dati su cui operano questi servizi.
