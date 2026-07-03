# Dashboard

La Dashboard è la pagina di atterraggio del pannello Clearance. Offre un riepilogo a colpo d'occhio dello stato di autorizzazione della tua applicazione - nessuna configurazione richiesta.

## Cosa mostra

- **Ruoli totali** - conteggio di tutti i ruoli Spatie.
- **Guardie totali** - conteggio di tutte le guardie gestite da Clearance (vedi [Guardie](/it/features/guards)).
- **Permessi totali** - conteggio di tutti i permessi Spatie.
- **Gruppi di permessi** - il numero di gruppi di permessi distinti, derivato dal prefisso di naming (il `group` in `group-action`, es. `orders` in `orders-create`). Vedi [Permessi](/it/features/permissions) per la convenzione di naming.
- **Assegnazioni di ruolo contestuali** - il numero di assegnazioni di ruolo limitate a una specifica istanza di modello (uno Store, un Tenant, un Project). Vedi [Contextual Authorization](/it/features/contextual-authorization).
- **Top 5 ruoli per numero di utenti** - i cinque ruoli con più utenti assegnati, classificati.

<Screenshot src="/screenshots/dashboard-page.png" alt="Dashboard di Clearance che mostra i conteggi di ruoli, guardie e permessi" caption="La Dashboard riassume ruoli, guardie, permessi, gruppi, assegnazioni contestuali, e i ruoli principali." />

## Accedere alla Dashboard

| URI | Nome route | Descrizione |
|---|---|---|
| `/clearance` | `clearance.home` | Dashboard |

Leggere la Dashboard richiede il permesso `clearance-access`, come ogni altra sezione del pannello. Vedi [Super Admin](/it/features/super-admin#panel-access-fine-grained-write-permissions) per la tabella completa di controllo accessi.

## Incorporare il componente

La Dashboard è un componente Livewire autonomo. Montala ovunque nei tuoi layout, in aggiunta - o in alternativa - alla route predefinita `/clearance`:

```blade
<livewire:clearance::dashboard />
```

## Prossimi passi

- [Permessi](/it/features/permissions) - gestisci l'elenco di permessi che alimenta i conteggi della Dashboard.
- [Ruoli](/it/features/roles) - gestisci i ruoli dietro la statistica "top 5 per numero di utenti".
- [Contextual Authorization](/it/features/contextual-authorization) - cos'è un'assegnazione di ruolo contestuale.
