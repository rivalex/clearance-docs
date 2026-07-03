# Architettura

## Come funziona la risoluzione

`resolveFor()` è il cuore dell'autorizzazione contestuale di Clearance - vedi [Contextual Authorization](/it/features/contextual-authorization) per l'API completa. Unisce tre fonti, in questo ordine di precedenza:

1. **Concessioni di ruolo contestuali** - permessi dai ruoli che l'utente detiene specificamente in questo contesto (`clr_role_ctx`).
2. **Concessioni di ruolo globali** - permessi dai ruoli Spatie assegnati globalmente all'utente (così `super_admin` e altri ruoli globali soddisfano `canIn()` per qualsiasi contesto).
3. **Override per contesto** (`clr_ctx_overrides`) - `forced_on` aggiunge un permesso oltre a quanto sopra; `forced_off` lo rimuove, anche se il permesso era stato concesso da un ruolo globale. Un diniego vince sempre.

::: warning Un diniego vince sempre
`forced_off` prevale su qualsiasi permesso concesso da un ruolo globale o da una concessione di ruolo contestuale. Non esiste modo di "superare in priorità" un override `forced_off` per lo stesso utente/permesso/contesto se non rimuovendolo.
:::

## Database

Clearance possiede 6 tabelle e non altera mai le tabelle core di Spatie (`roles`, `permissions`, `model_has_roles`, `model_has_permissions`, `role_has_permissions`). Tutte le FK che referenziano `roles.id`/`permissions.id` fanno cascade on delete salvo diversa indicazione; le tabelle di Spatie non hanno FK che puntano verso `clr_*`.

| Tabella | Colonne chiave |
|---|---|
| `clr_meta` | `subject_type`, `subject_key` (unici insieme) - `display_name`, `description`, `icon_svg`, `color` - metadati di visualizzazione per un ruolo o una guardia |
| `clr_role_meta` | `role_id` (unico, FK -> `roles`) - `is_locked`, `scope` (`global`\|`contextual`), `context_types` (json) - `parent_role_id` (FK nullabile -> `roles`, `ON DELETE SET NULL`) - ruolo ceiling |
| `clr_guards` | `name` (unico), `driver`, `provider` - guardie gestite tramite il pannello |
| `clr_settings` | `key` (unico), `value` - archivio chiave/valore a runtime (ruolo predefinito, ecc.) |
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - assegnazioni di ruolo contestuali, uniche per `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) - override dei permessi per utente e per contesto |

## Non tocca mai le tabelle di Spatie

Clearance è progettato per convivere accanto a `spatie/laravel-permission`, non per sostituirlo. Ogni record `Role` e `Permission` che un pannello Clearance crea o modifica è un genuino modello Spatie, memorizzato nelle tabelle proprie di Spatie. Clearance non esegue mai una migrazione che aggiunga una colonna a, o scriva una FK da, una qualsiasi tra `roles`, `permissions`, `model_has_roles`, `model_has_permissions`, o `role_has_permissions`.

Tutti i dati propri di Clearance - metadati di visualizzazione, ambito e ceiling dei ruoli, assegnazioni contestuali, override per contesto, definizioni delle guardie e impostazioni - vivono esclusivamente nelle 6 tabelle `clr_*` sopra elencate. Questo significa:

- Disinstallare Clearance non rischia mai di corrompere i dati esistenti di ruoli/permessi Spatie.
- Qualsiasi codice che già legge direttamente i modelli o le tabelle di Spatie continua a funzionare senza modifiche.
- Le funzionalità contestuali e di ceiling di Clearance sono livelli puramente additivi risolti a runtime (tramite `resolveFor()` e `RoleService`), non incorporati nello schema di Spatie.

## Correlati

- [Contextual Authorization](/it/features/contextual-authorization) - `canIn()`, `hasRoleIn()`, direttive Blade, `ContextService`
- [Roles](/it/features/roles) - ambito e ruoli ceiling, `RoleService::setParent()`
- [Migration](/it/guide/migration) - backfill di `clr_meta`/`clr_role_meta`/`clr_guards` su un'installazione Spatie esistente
- [Security](/it/guide/security) - come il modello di ceiling e override previene l'escalation di privilegi
