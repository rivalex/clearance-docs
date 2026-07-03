# Impostazioni

Il pannello Impostazioni configura come ruoli e guardie vengono visualizzati nel resto del pannello Clearance, oltre al ruolo predefinito assegnato ai nuovi utenti.

## Cosa fa il pannello

- **Metadati di visualizzazione** per ruolo/guardia - nome visualizzato, descrizione, e un'icona SVG sanitizzata + colore esadecimale, mostrati in tutto il pannello.
- **Ruolo predefinito** - il ruolo assegnato automaticamente ai nuovi utenti registrati quando `auto_assign_default_role` è `true`.
- **Assegnazione massiva del ruolo predefinito** - assegnazione con un clic del ruolo predefinito corrente a ogni utente esistente che non lo ha già.
- **Mostra icone** - attiva/disattiva la visualizzazione delle icone in tutto il pannello.

<Screenshot src="/screenshots/settings-page.png" alt="Pannello Impostazioni di Clearance con ruolo predefinito e interruttore mostra-icone" caption="Il pannello Impostazioni controlla il ruolo predefinito, l'assegnazione massiva, e la visualizzazione delle icone." />

## Metadati di visualizzazione per ruolo/guardia

<Screenshot src="/screenshots/settings-role-meta.png" alt="Form dei metadati di visualizzazione del ruolo con selettori di icona e colore" caption="Modifica del nome visualizzato, descrizione, icona, e colore per un ruolo." />

Ogni icona SVG fornita dall'utente passa attraverso un sanitizzatore allow-list prima della memorizzazione e di nuovo in fase di rendering, così i metadati di visualizzazione possono essere modificati senza introdurre markup non sicuro nel pannello.

## Ruolo predefinito e auto-assegnazione

```php
// config/clearance.php
'auto_assign_default_role' => true, // requires a default role set in Settings
```

::: tip Imposta il ruolo predefinito prima di abilitare l'auto-assegnazione
`auto_assign_default_role` non ha alcun effetto finché non viene configurato un ruolo predefinito nel pannello Impostazioni. Imposta prima il ruolo predefinito, poi abilita il flag.
:::

Usa **Assegnazione massiva** nel pannello in qualsiasi momento per assegnare retroattivamente il ruolo predefinito corrente agli utenti esistenti che non lo hanno già - utile dopo aver cambiato il ruolo predefinito, o dopo aver abilitato `auto_assign_default_role` su un'applicazione con utenti già esistenti.

## Controllo degli accessi

L'accesso in lettura richiede `clearance-access`. Modificare i metadati di visualizzazione, le icone, i colori, o il ruolo predefinito richiede `clearance-settings-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-settings-write');
```

## Route e componente incorporato

| URI | Nome route | Descrizione |
|---|---|---|
| `/clearance/settings` | `clearance.settings` | Metadati di visualizzazione (icone, colori), ruolo predefinito, impostazioni generali |

```blade
<livewire:clearance::settings.settings-manager />
```

## Database

| Tabella | Colonne chiave |
|---|---|
| `clr_meta` | `subject_type`, `subject_key` (unici insieme) - `display_name`, `description`, `icon_svg`, `color` |
| `clr_settings` | `key` (unico), `value` |

## Prossimi passi

- [Ruoli](/it/features/roles) - i ruoli i cui metadati di visualizzazione sono configurati qui.
- [Guardie](/it/features/guards) - le guardie i cui metadati di visualizzazione sono configurati qui.
- [Security](/it/guide/security) - come funziona il sanitizzatore SVG.
