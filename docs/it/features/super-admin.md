# Super Admin

Il ruolo `super_admin` viene creato automaticamente da `clearance:install` e mantenuto sincronizzato con tutti i permessi `clearance-*`.

```bash
# Install and make user ID 1 a super admin
php artisan clearance:install --user=1
```

Vedi la [guida all'installazione](/it/guide/installation) e il [riferimento dei comandi Artisan](/it/api/artisan-commands) per l'elenco completo dei flag dell'installer.

## Bypass del Gate (opzionale)

`super_admin_gate_bypass` controlla se il ruolo `super_admin` bypassa tutti i controlli Laravel `can()` / `Gate::allows()` / delle policy a livello globale, tramite un hook `Gate::before()`. Predefinito: `false`. Il bypass è opzionale. Per abilitarlo:

```php
// config/clearance.php
'super_admin_gate_bypass' => true,
```

::: danger A livello di applicazione, non limitato al pannello
Quando abilitato, qualsiasi utente che detiene `super_admin` salta **tutti** i controlli Gate a livello di applicazione - non solo i controlli del pannello Clearance. Questo interessa ogni `can()`, `Gate::allows()`, e controllo di policy nella tua applicazione, non solo quelli definiti da Clearance. Per un ambito più ristretto, lascialo disattivato e concedi i permessi esplicitamente.
:::

## Rilevamento degli alias

Se il tuo database contiene già un ruolo chiamato `super_admin`, `superadmin`, `super-admin`, `root`, o `owner`, l'installer lo rileva e ti chiede interattivamente se promuoverlo o creare un `super_admin` separato. Usa `--super-admin-role=NAME` per saltare il prompt:

```bash
php artisan clearance:install --super-admin-role=owner
```

::: tip La promozione è non distruttiva
La promozione rinomina il ruolo sul posto - tutte le assegnazioni `model_has_roles` esistenti vengono preservate tramite la FK su `role_id`. Nessun utente perde la propria assegnazione di ruolo esistente durante la promozione.
:::

## Accesso al pannello e permessi di scrittura granulari

Naviga verso `/clearance` dopo l'installazione. L'accesso in lettura all'intero pannello richiede il permesso `clearance-access`, verificato tramite `can()`, mai `hasRole()`:

```php
$user->givePermissionTo('clearance-access');
```

Le operazioni di scrittura su ogni sezione sono protette da un permesso dedicato e delegabile - tutti creati da `clearance:install` e assegnati a `super_admin`:

| Permesso | Controlla |
|---|---|
| `clearance-permissions-write` | Creazione / modifica / eliminazione dei permessi |
| `clearance-roles-write` | Creazione / modifica / eliminazione dei ruoli e delle loro assegnazioni di permessi |
| `clearance-guards-write` | Creazione / modifica / eliminazione delle guardie |
| `clearance-settings-write` | Modifica dei metadati di visualizzazione, icone, colori, ruolo predefinito |
| `clearance-users-write` | Assegnazione / rimozione dei ruoli per uno specifico utente |

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

::: tip Concedere un accesso ristretto
Combina `clearance-access` con esattamente un permesso `clearance-{section}-write` per dare a un utente (ad esempio, un team lead) accesso in scrittura a una singola sezione del pannello - i ruoli, ad esempio - senza esporre impostazioni, guardie, o gestione utenti.
:::

Se una riga di permesso granulare non esiste affatto nel database (uno stato legacy pre-seeding), il controllo ricade su `clearance-access` per retrocompatibilità. Su qualsiasi istanza che ha eseguito `clearance:install`, questo fallback non si attiva mai.

## Permessi `clearance-*` riservati

I permessi `clearance-*` sono riservati: non possono essere creati, rinominati, eliminati, o aggiunti a nessun ruolo se non attraverso il flusso di installazione proprio del pacchetto - nemmeno da un attore `super_admin` per il ruolo `super_admin` stesso.

## Prossimi passi

- [Permissions](/it/features/permissions) - il pannello CRUD generale dei permessi (esclude `clearance-*`).
- [Roles](/it/features/roles) - assegnare i permessi `clearance-*-write` a un ruolo personalizzato.
- [Security](/it/guide/security) - il modello completo di escalation di privilegi e ceiling.
