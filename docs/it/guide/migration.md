# Migrazione da un'installazione Spatie esistente

Se la tua applicazione usa già `spatie/laravel-permission` con ruoli, permessi e assegnazioni esistenti, `clearance:install` li rileva e li preserva - non esegue mai le migrazioni di Spatie se la tabella `roles` esiste già. Vedi [Installazione](/it/guide/installation) per il flusso completo di installazione.

Una volta installato, usa `clearance:backfill` per applicare i metadati propri di Clearance ai tuoi ruoli e guardie esistenti.

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

## Cosa fa ogni sezione

| Sezione | Effetto |
|---|---|
| `meta` | Genera un nome visualizzato `clr_meta` (nome del ruolo in title-case) per ogni ruolo che non ne ha uno. |
| `roles` | Genera i valori predefiniti di `clr_role_meta` (`scope=global`, `is_locked=false`) per ogni ruolo che non ne ha uno. |
| `guards` | Importa le guardie da `config('auth.guards')` in `clr_guards`, filtrate da `allowed_guard_drivers`. |

Usa `--only=<section>` per eseguire una singola sezione invece di tutte e tre.

::: tip Idempotente
Tutte le sezioni sono idempotenti - ri-eseguire `clearance:backfill` è sicuro e non duplicherà né sovrascriverà le righe `clr_*` esistenti.
:::

## Casi limite

Il repository del pacchetto contiene una guida di migrazione più approfondita che copre casi limite non riassunti qui - in particolare cosa succede quando il tuo database ha già un ruolo chiamato `super_admin` (o un alias come `superadmin`, `root`, `owner`) che entra in collisione con il ruolo `super_admin` proprio di Clearance, e note sulle sottoclassi personalizzate dei modelli `Role`/`Permission` e sul comportamento di importazione delle guardie. Leggila prima di eseguire il backfill su un database di produzione con dati di ruolo esistenti non banali. Vedi anche [Super Admin](/it/features/super-admin) per il comportamento di rilevamento degli alias durante `clearance:install`.

## Correlati

- [Installazione](/it/guide/installation) - il flusso completo di `clearance:install`
- [Comandi Artisan](/it/api/artisan-commands) - riferimento completo dei comandi
- [Architettura](/it/guide/architecture) - le tabelle `clr_*` che il backfill popola
