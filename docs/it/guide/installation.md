# Installazione

## Requisiti

Vedi la [tabella completa dei requisiti](/it/guide/introduction#requirements) nella pagina Introduzione - PHP ^8.3, Laravel ^11.0|^12.0|^13.0, spatie/laravel-permission ^6.0, livewire/livewire ^3.0|^4.0, livewire/flux ^2.14.

## Installa il pacchetto

```bash
composer require rivalex/clearance
```

## Esegui l'installer

```bash
php artisan clearance:install
```

::: danger Esegui sempre clearance:install, mai una semplice migrate
Le migrazioni di Clearance dipendono dall'esistenza preventiva delle tabelle `roles`/`permissions` di Spatie; l'installer pubblica ed esegue le migrazioni di Spatie prima delle proprie. Se esegui direttamente `php artisan migrate` su un'installazione nuova e Spatie non è ancora configurato, riceverai un messaggio di errore chiaro che ti dice di eseguire l'installer invece.
:::

### Flag opzionali

| Flag | Descrizione |
|---|---|
| `--user=ID` | Assegna il ruolo `super_admin` a un utente dopo l'installazione |
| `--role=NAME` | Assegna `clearance-access` a un ruolo (creato se assente) |
| `--super-admin-role=NAME` | Promuove un ruolo esistente a `super_admin` (salta il prompt interattivo) |
| `--force` | Ri-esegue anche se già installato |

### Cosa fa l'installer

1. Pubblica `config/clearance.php`.
2. Pubblica le migrazioni di Clearance.
3. Rileva le tabelle di Spatie Permission - se la tabella `roles` è assente, pubblica ed esegue automaticamente prima le migrazioni di Spatie.
4. Esegue tutte le migrazioni pendenti.
5. Crea `clearance-access` e tutti e 5 i permessi di capacità granulari: `clearance-permissions-write`, `clearance-roles-write`, `clearance-guards-write`, `clearance-settings-write`, `clearance-users-write`.
6. Crea il ruolo `super_admin` con tutti i permessi `clearance-*` assegnati (additivo - non rimuove mai i permessi esistenti in caso di ri-esecuzione).
7. Scrive `storage/.clearance-installed` come marcatore di idempotenza.

::: tip Stai migrando da un'installazione Spatie esistente?
Usa `php artisan clearance:backfill` invece di ripartire da zero, e leggi prima la [guida alla migrazione](/it/guide/migration) - copre la collisione con `super_admin`, le sottoclassi di modello personalizzate, e l'importazione delle guardie.
:::

## Aggiungi HasClearance al tuo modello User

Per usare i metodi di autorizzazione contestuale (`$user->canIn()`, `$user->hasRoleIn()`), aggiungi il trait `HasClearance` a `App\Models\User`. Include già `HasRoles` di Spatie - una riga sostituisce entrambi:

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance; // includes HasRoles + all contextual authorization methods
}
```

::: tip Usi già `use HasRoles`?
Sostituiscilo con `use HasClearance`, oppure mantieni entrambi - PHP deduplica automaticamente la composizione dei trait, nessun conflitto.
:::

Senza questo trait, `$user->canIn()` e i metodi correlati non sono disponibili, ma le direttive Blade (`@canin`, `@hasrolein`) e `ContextService` continuano a funzionare comunque - non dipendono dal trait. Vedi [Contextual Authorization](/it/features/contextual-authorization) per i dettagli.

## Prossimi passi

Continua con la [Guida Rapida](/it/guide/quick-start) per montare il pannello e iniziare a gestire i permessi.
