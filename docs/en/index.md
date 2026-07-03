---
layout: home

hero:
  name: "CLEARANCE"
  text: "Permissions, roles & contextual authorization for Laravel"
  tagline: A drop-in Livewire 4 + Flux UI admin panel on top of spatie/laravel-permission. Contextual authorization, ceiling roles, fine-grained delegation - installable with one artisan command, zero changes to Spatie's tables.
  image:
    light: /clearance-mark-horizontal.svg
    dark: /clearance-mark-horizontal.svg
    alt: Clearance
  actions:
    - theme: brand
      text: Get Started
      link: /en/guide/introduction
    - theme: alt
      text: View on GitHub
      link: https://github.com/rivalex/clearance

features:
  - icon: 🖥️
    title: Full admin panel
    details: Permissions, Roles, Guards, Settings and a Dashboard - use the pre-built routes or embed each Livewire component individually anywhere in your app.

  - icon: 🎯
    title: Contextual authorization
    details: "Grant a role or permission scoped to a specific model instance - a Store, a Tenant, a Project - with $user->canIn(), @canin/@hasrolein Blade directives, and a ContextService."

  - icon: 🏔️
    title: Ceiling roles
    details: A role can declare a parent role whose permission set acts as an upper bound. A child role can never exceed its parent, enforced automatically on every save.

  - icon: 🔑
    title: Fine-grained write permissions
    details: One delegable clearance-{section}-write permission per panel section, so you can hand out "manage roles" without also handing out "manage settings."

  - icon: 👑
    title: Super Admin, opt-in
    details: An opt-in global Gate bypass, alias detection for existing admin-like roles, and safe non-destructive promotion of an existing role.

  - icon: 🗄️
    title: Never touches Spatie's tables
    details: Clearance owns 6 dedicated clr_* tables and never alters roles, permissions, model_has_roles, model_has_permissions or role_has_permissions.

  - icon: 🛡️
    title: Security-hardened
    details: Privilege-escalation ceilings on every permission-granting path, deny-authoritative context overrides, an allow-list SVG sanitizer, and a protected clearance-* namespace.

  - icon: 👥
    title: Users module (opt-in)
    details: Assign roles globally or to a specific context, plus per-context permission overrides, from a per-user panel.

  - icon: 🌐
    title: 9 bundled languages
    details: "ar, en, es, fr, hi, it, pt, ru, zh - key-parity tested on every change."
---
