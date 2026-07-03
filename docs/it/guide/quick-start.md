# Guida Rapida

Dopo l'[installazione](/it/guide/installation), hai due modi per esporre il pannello Clearance: le route predefinite, oppure incorporare singoli componenti Livewire ovunque nella tua app.

## Opzione A - Route predefinite

Dopo l'installazione il pannello è disponibile su `/clearance` (o sul `route_prefix` configurato, vedi [Configurazione](/it/guide/configuration)). Tutte le route sono protette dal middleware `clearance.access` e usano automaticamente il layout Livewire dell'app ospitante.

| URI | Nome route | Descrizione |
|---|---|---|
| `/clearance` | `clearance.home` | Dashboard |
| `/clearance/guards` | `clearance.guards` | Gestione guardie (creazione/modifica/eliminazione) |
| `/clearance/permissions` | `clearance.permissions` | CRUD permessi con validazione del naming a prefisso di gruppo |
| `/clearance/roles` | `clearance.roles` | CRUD ruoli con assegnazione permessi vincolata alla guardia, ambito (globale/contestuale), e ceiling |
| `/clearance/settings` | `clearance.settings` | Metadati di visualizzazione (icone, colori), ruolo predefinito, impostazioni generali |
| `/clearance/user/{userId}` | `clearance.user` | Assegnazione ruoli per singolo utente + override dei permessi contestuali (richiede `modules.users = true`) |

I nomi delle route restano stabili indipendentemente dalle modifiche al prefisso. Per personalizzare il middleware:

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

## Opzione B - Componenti Livewire incorporati

Ogni sezione del pannello può essere montata singolarmente all'interno di qualsiasi vista Blade, pagina Filament, o route personalizzata:

```blade
<livewire:clearance::dashboard />
<livewire:clearance::permissions.permission-manager />
<livewire:clearance::roles.role-manager />
<livewire:clearance::guards.guard-manager />
<livewire:clearance::settings.settings-manager />
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

## Dove andare adesso

- [Riferimento di configurazione](/it/guide/configuration) - ogni chiave di configurazione, valori predefiniti e scopo
- [Contextual Authorization](/it/features/contextual-authorization) - limitare ruoli e permessi a uno Store, un Tenant, o un Project
- [Roles](/it/features/roles) - ambito e ruoli ceiling
- [Dashboard](/it/features/dashboard) - cosa mostra il componente Dashboard
- [Users](/it/features/users) - il pannello opzionale per singolo utente
