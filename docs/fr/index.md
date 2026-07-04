---
layout: home

hero:
  name: "CLEARANCE"
  text: "Permissions, rôles et autorisation contextuelle pour Laravel"
  tagline: Un panneau d'administration Livewire 4 + Flux UI prêt à l'emploi, construit au-dessus de spatie/laravel-permission. Autorisation contextuelle, rôles plafonds, délégation fine - installable en une seule commande artisan, sans aucune modification des tables de Spatie.
  image:
    light: /clearance-mark.svg
    dark: /clearance-mark.svg
    alt: Clearance
  actions:
    - theme: brand
      text: Commencer
      link: /fr/guide/introduction
    - theme: alt
      text: Voir sur GitHub
      link: https://github.com/rivalex/clearance

features:
  - icon: 🖥️
    title: Panneau d'administration complet
    details: Permissions, Rôles, Gardes, Paramètres et un Tableau de bord - utilisez les routes prêtes à l'emploi ou intégrez chaque composant Livewire individuellement n'importe où dans votre application.

  - icon: 🎯
    title: Autorisation contextuelle
    details: "Accordez un rôle ou une permission limité à une instance de modèle spécifique - un Store, un Tenant, un Project - avec $user->canIn(), les directives Blade @canin/@hasrolein, et un ContextService."

  - icon: 🏔️
    title: Rôles plafonds
    details: Un rôle peut déclarer un rôle parent dont l'ensemble de permissions agit comme une limite supérieure. Un rôle enfant ne peut jamais dépasser son parent, contrôle appliqué automatiquement à chaque enregistrement.

  - icon: 🔑
    title: Permissions d'écriture fines
    details: Une permission clearance-{section}-write délégable par section du panneau, afin de pouvoir confier "gérer les rôles" sans confier également "gérer les paramètres."

  - icon: 👑
    title: Super Admin, optionnel
    details: Un contournement de Gate global optionnel, une détection d'alias pour les rôles administrateur existants, et une promotion sûre et non destructive d'un rôle existant.

  - icon: 🗄️
    title: Ne touche jamais aux tables de Spatie
    details: Clearance possède 6 tables dédiées clr_* et ne modifie jamais roles, permissions, model_has_roles, model_has_permissions ni role_has_permissions.

  - icon: 🛡️
    title: Sécurité renforcée
    details: Plafonds anti-élévation de privilèges sur chaque chemin d'attribution de permission, surcharges de contexte à autorité de refus, un assainisseur SVG en liste blanche, et un espace de noms clearance-* protégé.

  - icon: 👥
    title: Module Utilisateurs (optionnel)
    details: Assignez des rôles globalement ou à un contexte spécifique, plus des surcharges de permissions par contexte, depuis un panneau par utilisateur.

  - icon: 🌐
    title: 9 langues incluses
    details: "ar, en, es, fr, hi, it, pt, ru, zh - parité des clés testée à chaque changement."

  - icon: 🧪
    title: 269 tests, 848 assertions
    details: Une suite Pest couvrant les commandes, services, composants Livewire, l'autorisation contextuelle, les rôles à ceiling et la parité i18n sur les 9 langues.
---
