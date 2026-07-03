# Ruoli

Il pannello Ruoli è un'interfaccia CRUD completa sul modello `Role` di Spatie, estesa con due concetti specifici di Clearance: **ambito** (globale vs. contestuale) e **ceiling** (un ruolo genitore che limita i permessi di un figlio).

## Cosa fa il pannello

- **Creazione / modifica / eliminazione** dei ruoli.
- **Assegnazione permessi vincolata alla guardia** - assegna permessi a un ruolo, filtrati in base alla guardia del ruolo.
- **Selezione dell'ambito** - `global` o `contextual`.
- **Configurazione del ceiling** - imposta o rimuovi un ruolo genitore.

<Screenshot src="/screenshots/roles-page.png" alt="Pannello Ruoli di Clearance che elenca i ruoli con ambito e ceiling" caption="Il pannello Ruoli elenca ogni ruolo con il suo ambito e, se impostato, il suo ceiling genitore." />

## Ambito: globale vs. contestuale

Ogni ruolo dichiara un ambito al momento della creazione:

- **`global`** - assegnato direttamente a un utente tramite `$user->assignRole()`, si applica ovunque.
- **`contextual`** - legato a uno o più tipi di modello (`context_types`), assegnabile solo all'interno di una specifica istanza di contesto tramite `UserRoleContext`. Vedi [Contextual Authorization](/it/features/contextual-authorization) e [Users](/it/features/users#contextual-roles-under-the-hood).

I ruoli senza una riga esplicita di `RoleMeta` hanno come predefinito `global`, per retrocompatibilità.

## Ruoli ceiling

Un ruolo può dichiarare un ceiling - un ruolo genitore il cui set di permessi funge da limite superiore:

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
```

<Screenshot src="/screenshots/role-form-ceiling.png" alt="Form del ruolo con un ruolo genitore ceiling selezionato" caption="Impostare un ruolo genitore ceiling nel form del Ruolo (RoleForm)." />

Regole applicate automaticamente ad ogni salvataggio:

- Il figlio può detenere solo permessi che il genitore detiene a sua volta. Ogni eccesso viene rimosso silenziosamente su `setParent()` e su ogni successiva sincronizzazione dei permessi - nessuna eccezione, nessun messaggio di errore, semplicemente applicato in modo silenzioso.
- Un ruolo che agisce già come genitore non può diventare figlio (`ClearanceScopeViolationException`).
- Un ruolo che è già figlio non può a sua volta agire come genitore (`ClearanceScopeViolationException`).
- La rimozione di permessi da un genitore propaga automaticamente la rimozione a ogni figlio.

::: warning Un ceiling rimuove soltanto, non concede mai
Impostare un genitore permissivo non concede di per sé alcun permesso al figlio. Il ceiling è strettamente un limite superiore - non aggiunge mai permessi al figlio, può solo rimuoverli.
:::

Questo si configura per ogni ruolo nel pannello Ruoli (`RoleForm`), oppure programmaticamente tramite `RoleService::setParent()` / `RoleService::removeParent()`.

## Controllo degli accessi

L'accesso in lettura richiede `clearance-access`. Creare, modificare, o eliminare un ruolo - incluse le sue assegnazioni di permessi, l'ambito, e il ceiling - richiede `clearance-roles-write`:

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

## Route e componente incorporato

| URI | Nome route | Descrizione |
|---|---|---|
| `/clearance/roles` | `clearance.roles` | CRUD ruoli con assegnazione permessi vincolata alla guardia, ambito (globale/contestuale), e ceiling |

```blade
<livewire:clearance::roles.role-manager />
```

## Database

I metadati del ruolo (ambito, ceiling, stato di blocco) vivono in `clr_role_meta`, mai nella tabella `roles` di Spatie:

| Tabella | Colonne chiave |
|---|---|
| `clr_role_meta` | `role_id` (unico, FK -> `roles`) - `is_locked`, `scope` (`global`\|`contextual`), `context_types` (json) - `parent_role_id` (FK nullabile -> `roles`, `ON DELETE SET NULL`) |

## Prossimi passi

- [Contextual Authorization](/it/features/contextual-authorization) - come vengono verificati a runtime i ruoli con `scope=contextual`.
- [Users](/it/features/users) - assegnare ruoli a uno specifico utente, globalmente o per contesto.
- [Riferimento Services](/it/api/services) - API completa di `RoleService`.
