# Sicurezza

Clearance è stato sottoposto a un audit di sicurezza dedicato pre-pubblicazione oltre a due successivi passaggi di hardening prima del rilascio. Punti salienti: limiti ceiling contro l'escalation di privilegi su ogni percorso di concessione dei permessi (concessioni dirette e sincronizzazione a livello di ruolo allo stesso modo), override di contesto con priorità al diniego, un sanitizzatore SVG allow-list, un namespace di permessi `clearance-*` protetto, e direttive Blade sicure per gli ospiti.

## Accesso al pannello e permessi di scrittura granulari

L'accesso in lettura all'intero pannello richiede il permesso `clearance-access` (verificato tramite `can()`, mai `hasRole()`). Le operazioni di scrittura su ogni sezione sono protette da un permesso dedicato e delegabile - tutti creati da `clearance:install` e assegnati a `super_admin`:

| Permesso | Controlla |
|---|---|
| `clearance-permissions-write` | Creazione / modifica / eliminazione dei permessi |
| `clearance-roles-write` | Creazione / modifica / eliminazione dei ruoli e delle loro assegnazioni di permessi |
| `clearance-guards-write` | Creazione / modifica / eliminazione delle guardie |
| `clearance-settings-write` | Modifica dei metadati di visualizzazione, icone, colori, ruolo predefinito |
| `clearance-users-write` | Assegnazione / rimozione dei ruoli per uno specifico utente |

Se una riga di permesso granulare non esiste affatto nel database (uno stato legacy pre-seeding), il controllo ricade su `clearance-access` per retrocompatibilità.

## Namespace `clearance-*` protetto

I permessi `clearance-*` sono riservati: non possono essere creati, rinominati, eliminati, o aggiunti a nessun ruolo se non attraverso il flusso di installazione proprio del pacchetto - nemmeno da un attore `super_admin` per il ruolo `super_admin` stesso.

## Limiti ceiling contro l'escalation di privilegi

Ogni percorso di concessione dei permessi in Clearance applica un ceiling in modo che un attore non possa mai concedere più di quanto detiene lui stesso:

- **Ruoli ceiling** - un ruolo può dichiarare un ruolo genitore il cui set di permessi funge da limite superiore. Ogni permesso in eccesso viene rimosso silenziosamente su `RoleService::setParent()` e su ogni successiva sincronizzazione dei permessi - nessuna eccezione, nessun messaggio di errore. La rimozione di permessi da un genitore propaga automaticamente la rimozione a ogni figlio. Vedi [Roles](/it/features/roles) per l'API completa del ceiling.
- **Modulo Utenti** - sia l'assegnazione dei ruoli che le concessioni dirette/override dei permessi applicano un ceiling di privilegio: un attore non `super_admin` non può modificare le proprie assegnazioni, e non può concedere un permesso che non detiene lui stesso. Vedi [Users](/it/features/users).

## Override di contesto con priorità al diniego

Gli override dei permessi per contesto (`clr_ctx_overrides`) supportano `forced_on` e `forced_off`. `forced_off` vince sempre - anche su un permesso concesso da un ruolo globale. Non esiste un percorso che permetta a una concessione di priorità inferiore di prevalere su un diniego esplicito. Vedi [Architecture](/it/guide/architecture#how-resolution-works) per l'ordine completo di risoluzione.

## Sanitizzazione SVG

Ogni icona fornita dall'utente (metadati di visualizzazione di ruolo/guardia nel pannello Impostazioni) passa attraverso un sanitizzatore allow-list sia prima della memorizzazione sia in fase di rendering - non un controllo una tantum al momento del caricamento.

## Direttive Blade sicure per gli ospiti

`@canin` e `@hasrolein` si risolvono entrambe in modo sicuro (restituiscono `false`) per un utente ospite - non lanciano mai un'eccezione.

## Il bypass del Gate è a livello di applicazione

`super_admin_gate_bypass` (predefinito `false`) controlla se il ruolo `super_admin` bypassa tutti i controlli Laravel `can()` / `Gate::allows()` / delle policy a livello globale, tramite un hook `Gate::before()`.

::: warning Non limitato al pannello
Quando abilitato, qualsiasi utente che detiene `super_admin` salta tutti i controlli Gate a livello di applicazione - non solo i controlli del pannello Clearance. Abilitalo deliberatamente. Vedi [Super Admin](/it/features/super-admin) per il rilevamento degli alias e il comportamento di promozione.
:::

## Accortezze rilevanti per la sicurezza

- `forced_off` vince sempre - anche su un permesso concesso da un ruolo globale.
- Un ruolo ceiling rimuove soltanto, non concede mai - impostare un genitore permissivo non concede di per sé alcun permesso al figlio.
- Il bypass del Gate (`super_admin_gate_bypass`) è a livello di applicazione, non limitato al pannello.
- Il modulo Utenti è disattivato per impostazione predefinita (`modules.users = false`).
- I permessi `clearance-*` sono intoccabili al di fuori del flusso di installazione, anche per `super_admin`.
- Esegui sempre `clearance:install`, non una semplice `migrate`, su un'installazione nuova - vedi [Installation](/it/guide/installation).

## Correlati

- [Architecture](/it/guide/architecture) - precedenza di risoluzione e schema `clr_*`
- [Roles](/it/features/roles) - i ruoli ceiling in dettaglio
- [Users](/it/features/users) - il ceiling di privilegio sull'assegnazione di ruoli/permessi
- [Super Admin](/it/features/super-admin) - bypass del Gate e rilevamento degli alias
