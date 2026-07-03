# Pubblicazione

Clearance pubblica tre tipi di asset tramite i tag `vendor:publish`: configurazione, migrazioni, e traduzioni. Configurazione e migrazioni sono gestite automaticamente dall'installer (vedi [Installation](/it/guide/installation)); questa pagina copre la loro pubblicazione manuale, e la pubblicazione delle traduzioni.

## Configurazione

```bash
php artisan vendor:publish --tag=clearance-config
```

Pubblica `config/clearance.php` nella tua applicazione. Vedi il [riferimento di configurazione](/it/guide/configuration) per ogni chiave, il suo valore predefinito, e il suo scopo.

## Traduzioni

```bash
php artisan vendor:publish --tag=clearance-translations
```

Questo copia le 9 lingue incluse (`ar`, `en`, `es`, `fr`, `hi`, `it`, `pt`, `ru`, `zh`) in `lang/vendor/clearance/{locale}/ui.php` nella tua app. Per aggiungere una nuova lingua o sovrascrivere le stringhe in una esistente, aggiungi lì una directory per la lingua, usando `en/ui.php` come riferimento canonico delle chiavi. Tutte le lingue espongono un insieme identico di chiavi, verificato da un test dedicato ad ogni modifica.

## Sovrascrivere il layout del pannello

La chiave di configurazione `layout` controlla il layout Blade usato dai componenti a pagina intera:

```php
// config/clearance.php
'layout' => null, // host app's default (config('livewire.layout'))
```

Lasciala a `null` per usare il layout Livewire predefinito della tua applicazione, oppure impostala su una vista di layout specifica per renderizzare il pannello dentro una shell personalizzata.

## Migrazioni

Le migrazioni di Clearance vengono pubblicate ed eseguite automaticamente da `php artisan clearance:install` - l'installer pubblica ed esegue anche le migrazioni di Spatie prima, se la tabella `roles` non esiste ancora. Vedi [Installation](/it/guide/installation) per il flusso completo passo-passo, e il [riquadro di pericolo](/it/guide/installation#run-the-installer) sul non eseguire mai una semplice `migrate` su un'installazione nuova.

## Correlati

- [Riferimento di configurazione](/it/guide/configuration) - ogni chiave di configurazione
- [Installation](/it/guide/installation) - il flusso completo dell'installer
- [Migration](/it/guide/migration) - `clearance:backfill` per installazioni Spatie esistenti
