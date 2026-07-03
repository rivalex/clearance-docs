# Introduzione

## Cos'è Clearance?

Clearance è un pannello di amministrazione pronto all'uso per applicazioni Laravel, costruito sopra [spatie/laravel-permission](https://github.com/spatie/laravel-permission). Aggiunge un pannello Livewire 4 + Flux UI per gestire permessi, ruoli, guardie e impostazioni, oltre a un livello di autorizzazione contestuale e ruoli ceiling - installabile con un solo comando artisan, senza alterare alcuna tabella di Spatie.

Clearance ti offre:

- **Pannello di amministrazione** per Permessi, Ruoli, Guardie, Impostazioni e una Dashboard - usa le route predefinite oppure incorpora ogni componente Livewire singolarmente.
- **Autorizzazione contestuale** - concedi un ruolo o un permesso limitato a una specifica istanza di modello (uno Store, un Tenant, un Project), con `$user->canIn()`, le direttive Blade `@canin`/`@hasrolein`, e un `ContextService` da usare al di fuori del modello User.
- **Ruoli ceiling** - un ruolo può dichiarare un ruolo genitore il cui set di permessi funge da limite superiore; un ruolo figlio non può mai superare il proprio genitore, applicato automaticamente ad ogni salvataggio.
- **Permessi di scrittura granulari** - un permesso delegabile `clearance-{section}-write` per ogni sezione del pannello, così puoi concedere "gestisci ruoli" senza concedere anche "gestisci impostazioni".
- **Super Admin** con un bypass globale del Gate opzionale, rilevamento di alias per ruoli amministrativi già esistenti, e promozione sicura e non distruttiva.
- **Modulo Utenti** (opzionale) - assegna ruoli globalmente o a un contesto specifico, oltre a override dei permessi per contesto, da un pannello per singolo utente.
- **Pannello Impostazioni** - nome visualizzato, descrizione, icona SVG sanitizzata e colore per ogni ruolo/guardia; un ruolo predefinito configurabile con auto-assegnazione e assegnazione massiva.
- **Gestione Guardie** - guardie basate su database, iniettate in `auth.guards` all'avvio, limitate a un insieme allow-listed di driver.
- **9 lingue incluse** (ar, en, es, fr, hi, it, pt, ru, zh), testate per parità di chiavi.
- **Hook di estensibilità** - trait `HasClearance`, trait `HasPermissionGroups`, contratto `ClearanceContextable`, componenti Blade riutilizzabili.
- **Sanitizzazione SVG** - ogni icona fornita dall'utente passa attraverso un sanitizzatore allow-list prima della memorizzazione e in fase di rendering.
- **Sicurezza rafforzata** - limiti ceiling contro l'escalation di privilegi su ogni percorso di concessione dei permessi, override di contesto con priorità al diniego, namespace `clearance-*` protetto. Vedi [Security](/it/guide/security).

## Relazione con spatie/laravel-permission

Clearance non è un sostituto del pacchetto di permessi di Spatie - è un livello di UI e autorizzazione costruito sopra di esso. Ruoli e permessi restano i modelli `Role` e `Permission` di Spatie, ancora memorizzati nelle tabelle `roles`, `permissions`, `model_has_roles`, `model_has_permissions` e `role_has_permissions` di Spatie. Clearance non altera mai quelle tabelle. Tutto ciò che Clearance aggiunge - metadati di visualizzazione, ambito del ruolo, ceiling, assegnazioni contestuali, override per contesto - vive nel proprio insieme di tabelle `clr_*`. Vedi [Architecture](/it/guide/architecture) per il quadro completo.

## Requisiti

| Dipendenza | Versione |
|---|---|
| PHP | ^8.3 |
| Laravel | ^11.0 \| ^12.0 \| ^13.0 |
| spatie/laravel-permission | ^6.0 |
| livewire/livewire | ^3.0 \| ^4.0 |
| livewire/flux | ^2.14 |

## Concetti chiave

| Concetto | Riepilogo |
|---|---|
| Permesso | Un permesso Spatie che segue la convenzione di naming `group-action` (es. `orders-create`). Concede un'abilità. |
| Ambito del ruolo | Ogni ruolo è `global` (assegnato direttamente, si applica ovunque) o `contextual` (legato a tipi di modello specifici, assegnabile solo all'interno di un'istanza di contesto). |
| Ruolo ceiling | Un ruolo può dichiarare un ruolo genitore il cui set di permessi è un limite superiore - il figlio non può mai superarlo. |
| Contesto | Un'istanza di modello (uno Store, un Tenant, un Project) a cui può essere limitato un ruolo o un override di permesso. |
| Override | Una concessione o un diniego di permesso `forced_on`/`forced_off` per utente e per contesto - un diniego vince sempre. |

Ognuno di questi concetti ha una pagina dedicata: [Permissions](/it/features/permissions), [Roles](/it/features/roles), [Contextual Authorization](/it/features/contextual-authorization), [Users](/it/features/users), [Super Admin](/it/features/super-admin).

## Prossimi passi

Pronto a installare Clearance? Continua con la [guida all'installazione](/it/guide/installation).
