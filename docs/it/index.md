---
layout: home

hero:
  name: "CLEARANCE"
  text: "Permessi, ruoli e autorizzazione contestuale per Laravel"
  tagline: Un pannello di amministrazione Livewire 4 + Flux UI pronto all'uso, costruito sopra spatie/laravel-permission. Autorizzazione contestuale, ruoli ceiling, delega granulare - installabile con un solo comando artisan, zero modifiche alle tabelle di Spatie.
  image:
    light: /clearance-mark.svg
    dark: /clearance-mark.svg
    alt: Clearance
  actions:
    - theme: brand
      text: Inizia
      link: /it/guide/introduction
    - theme: alt
      text: Vedi su GitHub
      link: https://github.com/rivalex/clearance

features:
  - icon: 🖥️
    title: Pannello di amministrazione completo
    details: Permessi, Ruoli, Guardie, Impostazioni e una Dashboard - usa le route predefinite oppure incorpora ogni componente Livewire singolarmente ovunque nella tua app.

  - icon: 🎯
    title: Autorizzazione contestuale
    details: "Concedi un ruolo o un permesso limitato a una specifica istanza di modello - uno Store, un Tenant, un Project - con $user->canIn(), le direttive Blade @canin/@hasrolein e un ContextService."

  - icon: 🏔️
    title: Ruoli ceiling
    details: Un ruolo può dichiarare un ruolo genitore il cui set di permessi funge da limite superiore. Un ruolo figlio non può mai superare il proprio genitore, applicato automaticamente ad ogni salvataggio.

  - icon: 🔑
    title: Permessi di scrittura granulari
    details: Un permesso delegabile clearance-{section}-write per ogni sezione del pannello, così puoi concedere "gestisci ruoli" senza concedere anche "gestisci impostazioni".

  - icon: 👑
    title: Super Admin, opzionale
    details: Un bypass globale del Gate opzionale, il rilevamento di alias per ruoli amministrativi già esistenti, e una promozione sicura e non distruttiva di un ruolo esistente.

  - icon: 🗄️
    title: Non tocca mai le tabelle di Spatie
    details: Clearance possiede 6 tabelle dedicate clr_* e non altera mai roles, permissions, model_has_roles, model_has_permissions o role_has_permissions.

  - icon: 🛡️
    title: Sicurezza rafforzata
    details: Limiti ceiling contro l'escalation di privilegi su ogni percorso di concessione dei permessi, override di contesto con priorità al diniego, un sanitizzatore SVG allow-list e un namespace clearance-* protetto.

  - icon: 👥
    title: Modulo Utenti (opzionale)
    details: Assegna ruoli globalmente o a un contesto specifico, oltre a override dei permessi per contesto, da un pannello per singolo utente.

  - icon: 🌐
    title: 9 lingue incluse
    details: "ar, en, es, fr, hi, it, pt, ru, zh - testate per parità di chiavi ad ogni modifica."

  - icon: 🧪
    title: 370 test, 1054 asserzioni
    details: Una suite Pest che copre comandi, servizi, componenti Livewire, autorizzazione contestuale, ruoli con ceiling e parità i18n su tutte le 9 lingue.
---
