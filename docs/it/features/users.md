# Utenti

Il modulo Utenti è un pannello per singolo utente per assegnare ruoli - globalmente o limitati a uno specifico contesto - e per concedere o negare permessi individuali all'interno di un contesto. È opzionale e disabilitato per impostazione predefinita.

## Abilitare il modulo

```php
// config/clearance.php
'modules' => ['users' => true],
```

## Cosa fa il pannello

- **Assegnazione di ruolo globale** - assegna ruoli con `scope=global` direttamente all'utente, a livello di applicazione.
- **Assegnazione di ruolo contestuale** - assegna ruoli con `scope=contextual` limitati a una specifica istanza di modello (uno Store, un Team, o un Project dichiarato in `contextual_models`).
- **Override dei permessi per contesto** - per ogni assegnazione di ruolo contestuale, permessi aggiuntivi possono essere forzati come concessi o negati indipendentemente per ogni istanza di contesto.
- **Rimozione dell'assegnazione** - rimuove un ruolo dall'utente (ambito globale o contestuale), propagando l'eliminazione a qualsiasi override associato.

<Screenshot src="/screenshots/user-panel-global.png" alt="Pannello utente che mostra l'assegnazione di un ruolo globale" caption="Assegnare un ruolo globale a un utente - si applica a livello di applicazione." />

<Screenshot src="/screenshots/user-panel-contextual.png" alt="Pannello utente che mostra l'assegnazione di un ruolo contestuale e gli override dei permessi per contesto" caption="Assegnare un ruolo contestuale e impostare override dei permessi per contesto per una specifica istanza di Store." />

::: warning Ceiling di privilegio sull'auto-modifica
Sia l'assegnazione dei ruoli sia le concessioni dirette/override dei permessi applicano un ceiling di privilegio: un attore non `super_admin` non può modificare le proprie assegnazioni, e non può concedere un permesso che non detiene lui stesso.
:::

## Controllo degli accessi

L'accesso a `/clearance/user/{userId}` richiede `clearance-access` (lettura) e `clearance-users-write` (assegnazione/rimozione):

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-users-write');
```

## Route e componente incorporato

| URI | Nome route | Descrizione |
|---|---|---|
| `/clearance/user/{userId}` | `clearance.user` | Assegnazione ruoli per singolo utente + override dei permessi contestuali (richiede `modules.users = true`) |

```blade
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

### Collegarsi al pannello dalla tua app

```php
route('clearance.user', ['userId' => $user->id])
```

## I ruoli contestuali sotto il cofano

Assegnare un ruolo contestuale a un utente crea una riga `UserRoleContext`:

```php
use Rivalex\Clearance\Models\UserRoleContext;

// Samuele is "StoreStaff" only within Alessandro's Store
UserRoleContext::create([
    'user_id'      => $samuele->id,
    'role_id'      => $staffRole->id,
    'context_type' => Store::class,
    'context_id'   => $store->id,
]);
```

I controlli di autorizzazione rispettano il contesto tramite `$user->canIn()`, `@canin`, e `ContextService`. Vedi [Contextual Authorization](/it/features/contextual-authorization) per il modello completo di risoluzione.

## Database

| Tabella | Colonne chiave |
|---|---|
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - unico per `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) |

## Prossimi passi

- [Contextual Authorization](/it/features/contextual-authorization) - la precedenza completa di risoluzione per `canIn()`.
- [Roles](/it/features/roles) - configurare l'ambito di un ruolo e i suoi `context_types`.
- [Riferimento di configurazione](/it/guide/configuration) - `modules.users`, `contextual_models`.
