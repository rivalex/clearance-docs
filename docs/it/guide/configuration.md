# Configurazione

Clearance viene fornito con impostazioni predefinite sensate, quindi funziona out of the box dopo l'[installazione](/it/guide/installation). Pubblica il file di configurazione quando devi personalizzare il prefisso delle route, il middleware, i modelli contestuali o qualsiasi altra impostazione.

```bash
php artisan vendor:publish --tag=clearance-config
```

Questo pubblica `config/clearance.php`.

## Riferimento di configurazione

Questo è il riferimento definitivo per ogni chiave in `config/clearance.php`. Le altre pagine di questa documentazione rimandano qui invece di ripetere la tabella.

| Chiave | Predefinito | Scopo |
|---|---|---|
| `route_prefix` | `'clearance'` | Prefisso URI per tutte le route del pannello. |
| `middleware` | `['web', 'auth']` | Applicato a tutte le route del pannello prima di `clearance.access`. |
| `access_permission` | `'clearance-access'` | Permesso verificato dal middleware di accesso. |
| `user_model` | `null` | Il valore predefinito è `auth.providers.users.model`. |
| `modules.users` | `false` | Interruttore principale per il modulo Utenti (route + componenti Livewire). |
| `contextual_models` | `[]` | `FQCN => ['label', 'icon', 'label_attribute', 'searchable']` - modelli che possono fungere da contesti di binding per i ruoli. |
| `enforce_naming_convention` | `true` | Applica la convenzione di naming `group-action` per i permessi. |
| `naming_separator` | `'-'` | Carattere separatore - solo `'-'` o `'_'`. |
| `guards` | `[]` | Sovrascrive le guardie rilevate automaticamente da `config/auth.php`. Vuoto = rileva automaticamente tutte. |
| `allowed_guard_drivers` | `['session', 'token', 'jwt', 'passport', 'sanctum']` | Driver accettati durante l'iniezione delle guardie basate su database in `auth.guards`. |
| `layout` | `null` | Layout Blade per i componenti a pagina intera. `null` = predefinito dell'app ospitante (`config('livewire.layout')`). |
| `auto_assign_default_role` | `false` | Assegna automaticamente il ruolo predefinito configurato nelle Impostazioni all'evento `Registered`. |
| `super_admin_gate_bypass` | `false` | Opzionale: `super_admin` bypassa tutti i controlli Gate a livello di applicazione. |
| `ui.flux_pro` | `null` | `null` = rilevamento automatico tramite `Flux::pro()`. |

## Convenzione di naming dei permessi

I permessi devono seguire il formato `group-action`:

```
orders-create       correct
orders-read         correct
store-orders-delete correct

create              wrong (bare action, no group)
orders.create       wrong (dot separator)
Orders-Create       wrong (camelCase)
```

Disabilita l'applicazione per ambiente con `enforce_naming_convention`:

```php
'enforce_naming_convention' => false,
```

::: warning Il separatore viene validato all'avvio
Il valore di `naming_separator` è l'unica parte di questa configurazione che raggiunge un frammento SQL grezzo (raggruppamento/ordinamento dei permessi), quindi sono accettati solo `-` o `_`.
:::

## Personalizzare il middleware

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

I nomi delle route restano stabili indipendentemente dalle modifiche al prefisso o al middleware - vedi la [Guida Rapida](/it/guide/quick-start) per la tabella completa delle route.

## Correlati

- [Guida Rapida](/it/guide/quick-start) - montare il pannello con queste impostazioni applicate
- [Pubblicazione degli asset](/it/guide/publishing) - pubblicare le traduzioni e capire la chiave `layout`
- [Guardie](/it/features/guards) - come vengono usate `guards` e `allowed_guard_drivers`
- [Super Admin](/it/features/super-admin) - come funziona `super_admin_gate_bypass`
