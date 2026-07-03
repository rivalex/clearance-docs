# Componenti Blade

Clearance registra un piccolo insieme di componenti Blade sotto il namespace `clearance::`. Sono tutti riutilizzabili direttamente nelle viste della tua applicazione, non solo all'interno del pannello.

## Componenti

### `x-clearance::message`

Visualizzazione di messaggi flash/di stato.

```blade
<x-clearance::message>
    Changes saved successfully.
</x-clearance::message>
```

### `x-clearance::clipboard`

Pulsante/wrapper copia-negli-appunti — usato, ad esempio, per mostrare nomi di route o ID con un'azione di copia con un clic.

```blade
<x-clearance::clipboard :value="$route->name" />
```

### `x-clearance::validation-errors`

Renderer standard dell'elenco di errori di validazione di Laravel.

```blade
<x-clearance::validation-errors />
```

### `x-clearance::icon.*`

Un piccolo set di icone incluso, usato in tutto il pannello. Nessuna dipendenza JS esterna oltre ad Alpine, già richiesta da Livewire.

```blade
<x-clearance::icon.check />
```

## Componenti Livewire

Per riferimento, questi sono i nomi completi dei componenti Livewire a pagina intera/incorporabili registrati dal pacchetto, utilizzabili con l'incorporamento `<livewire:clearance::...>` nelle tue viste:

| Nome componente | Descrizione |
|---|---|
| `dashboard` | La dashboard del pannello. |
| `permissions.permission-manager` | Pannello CRUD dei permessi. |
| `roles.role-manager` | Pannello CRUD dei ruoli, inclusi i ruoli ceiling. |
| `guards.guard-manager` | Pannello CRUD delle guardie. |
| `settings.settings-manager` | Pannello Impostazioni (metadati di visualizzazione, icone, colori). |
| `users.user-clearance-manager` | Pannello di assegnazione ruoli/permessi per singolo utente. Accetta `:userId`. |

```blade
<livewire:clearance::dashboard />

<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

Vedi le pagine di funzionalità corrispondenti per ogni pannello: [Dashboard](/it/features/dashboard.md), [Permissions](/it/features/permissions.md), [Roles](/it/features/roles.md), [Guards](/it/features/guards.md), [Settings](/it/features/settings.md), [Users](/it/features/users.md).

::: tip
La chiave `config('clearance.ui.flux_pro')` controlla se gli elementi Flux UI solo Pro vengono renderizzati in questi componenti. `null` (il valore predefinito) rileva automaticamente Flux Pro tramite `Flux::pro()`.
:::
