# Comandi Artisan

Clearance include due comandi Artisan: `clearance:install` per la prima configurazione, e `clearance:backfill` per generare i valori predefiniti su installazioni esistenti. Vedi la [guida all'installazione](/it/guide/installation.md) per il percorso completo di configurazione.

## `clearance:install`

```bash
php artisan clearance:install
php artisan clearance:install --user=1
php artisan clearance:install --role=editor
php artisan clearance:install --super-admin-role=owner
php artisan clearance:install --force
```

| Flag | Descrizione |
|---|---|
| `--user=ID` | Assegna il ruolo `super_admin` a un utente dopo l'installazione. |
| `--role=NAME` | Assegna `clearance-access` a un ruolo (creato se assente). |
| `--super-admin-role=NAME` | Promuove un ruolo esistente a `super_admin` (salta il prompt interattivo). |
| `--force` | Ri-esegue anche se già installato. |

### Cosa fa, in ordine

1. Pubblica `config/clearance.php`.
2. Pubblica le migrazioni di Clearance.
3. Rileva le tabelle di Spatie Permission — se la tabella `roles` è assente, pubblica ed esegue automaticamente prima le migrazioni di Spatie.
4. Esegue tutte le migrazioni pendenti.
5. Crea `clearance-access` e tutti e 5 i permessi di capacità granulari: `clearance-permissions-write`, `clearance-roles-write`, `clearance-guards-write`, `clearance-settings-write`, `clearance-users-write`.
6. Crea il ruolo `super_admin` con tutti i permessi `clearance-*` assegnati. Questo passaggio è additivo — non rimuove mai i permessi esistenti in caso di ri-esecuzione.
7. Scrive `storage/.clearance-installed` come marcatore di idempotenza.

### Rilevamento degli alias

Se il database contiene già un ruolo chiamato `super_admin`, `superadmin`, `super-admin`, `root`, o `owner`, l'installer lo rileva e chiede interattivamente se promuoverlo o creare un ruolo `super_admin` separato. Usa `--super-admin-role=NAME` per saltare il prompt in modo non interattivo.

La promozione rinomina il ruolo sul posto — tutte le assegnazioni `model_has_roles` vengono preservate tramite la foreign key su `role_id`.

::: warning
I permessi `clearance-*` sono protetti al di fuori del flusso di installazione — vedi [`PermissionService`](/it/api/services.md#permissionservice) e `ClearanceProtectedResourceException` in [Services](/it/api/services.md#exceptions).
:::

## `clearance:backfill`

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

| Flag | Descrizione |
|---|---|
| `--only=meta\|roles\|guards` | Esegue solo una sezione di backfill. |
| `--dry-run` | Anteprima delle modifiche senza scrivere. |

### Sezioni

| Sezione | Descrizione |
|---|---|
| `meta` | Genera un nome visualizzato `clr_meta` (nome del ruolo in title-case) per ogni ruolo che non ne ha uno. |
| `roles` | Genera i valori predefiniti di `clr_role_meta` (`scope=global`, `is_locked=false`) per ogni ruolo che non ne ha uno. |
| `guards` | Importa le guardie da `config('auth.guards')` in `clr_guards`, filtrate da `allowed_guard_drivers`. |

Tutte le sezioni sono idempotenti — eseguire ripetutamente `clearance:backfill` è sicuro e riempie solo ciò che manca.

::: tip
Per installazioni Spatie preesistenti: `clearance:install` non esegue mai le migrazioni di Spatie se la tabella `roles` esiste già, quindi ruoli, permessi, e assegnazioni esistenti vengono preservati automaticamente.
:::

## Vedi anche

- [Services](/it/api/services.md) — `GuardService` e `MetaService`, che supportano le sezioni di backfill `guards` e `meta`.
- [Migration guide](/it/guide/migration.md) — migrare un'installazione Spatie Permission esistente a Clearance.
