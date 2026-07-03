# Permessi

Il pannello Permessi è un'interfaccia CRUD completa sul modello `Permission` di Spatie. Clearance non introduce mai una propria tabella dei permessi - ogni permesso creato qui è un normale permesso Spatie, utilizzabile ovunque venga usata l'API di Spatie (`$user->can()`, `Gate::allows()`, policy, `@can`).

## Cosa fa il pannello

- **Creazione** - aggiungi un nuovo permesso, validato rispetto alla [convenzione di naming](#permission-naming-convention) (a meno che non sia disabilitata) e limitato a una guardia.
- **Modifica** - rinomina un permesso o cambia la sua guardia.
- **Eliminazione** - rimuovi un permesso e le sue assegnazioni a ruoli/utenti (comportamento standard di cascade di Spatie).

<Screenshot src="/screenshots/permissions-page.png" alt="Pannello Permessi di Clearance che elenca i permessi raggruppati per prefisso di naming" caption="Il pannello Permessi elenca ogni permesso Spatie, raggruppato per prefisso di naming." />

<Screenshot src="/screenshots/permission-form-modal.png" alt="Form di creazione/modifica permesso con validazione della convenzione di naming" caption="Il form dei permessi valida la convenzione di naming group-action prima di salvare." />

## Convenzione di naming dei permessi

I permessi devono seguire il formato `group-action`:

```
orders-create       correct
orders-read         correct
store-orders-delete correct

create              wrong (bare action, no group)
orders.create       wrong (dot separator)
Orders-Create       wrong (camelCase)
```

Il prefisso di gruppo (`orders` in `orders-create`) è ciò che la [Dashboard](/it/features/dashboard) usa per contare i gruppi di permessi distinti.

::: tip Disabilitare l'applicazione
Alcuni team migrano da uno schema di naming diverso. Disabilita il controllo per ambiente:

```php
// config/clearance.php
'enforce_naming_convention' => false,
```
:::

## Controllo degli accessi

L'accesso in lettura al pannello (visualizzazione dell'elenco dei permessi) richiede `clearance-access`. Creare, modificare, o eliminare un permesso richiede inoltre `clearance-permissions-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-permissions-write');
```

::: warning I permessi `clearance-*` sono riservati
I permessi `clearance-*` stessi (`clearance-access`, `clearance-permissions-write`, ecc.) non possono essere creati, rinominati, o eliminati tramite questo pannello - nemmeno da un attore `super_admin`. Sono gestiti esclusivamente dal flusso di installazione proprio del pacchetto. Vedi [Super Admin](/it/features/super-admin#reserved-clearance-permissions).
:::

## Route e componente incorporato

| URI | Nome route | Descrizione |
|---|---|---|
| `/clearance/permissions` | `clearance.permissions` | CRUD permessi con validazione del naming a prefisso di gruppo |

```blade
<livewire:clearance::permissions.permission-manager />
```

## Prossimi passi

- [Ruoli](/it/features/roles) - assegna permessi a un ruolo, limitati alla guardia.
- [Super Admin](/it/features/super-admin) - la tabella completa dei permessi di scrittura granulari.
- [Riferimento di configurazione](/it/guide/configuration) - `enforce_naming_convention`, `naming_separator`.
