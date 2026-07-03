---
layout: home

hero:
  name: "CLEARANCE"
  text: "Permisos, roles y autorización contextual para Laravel"
  tagline: Un panel de administración Livewire 4 + Flux UI listo para usar sobre spatie/laravel-permission. Autorización contextual, roles techo, delegación granular - instalable con un solo comando artisan, sin cambios en las tablas de Spatie.
  image:
    light: /clearance-mark-horizontal.svg
    dark: /clearance-mark-horizontal.svg
    alt: Clearance
  actions:
    - theme: brand
      text: Comenzar
      link: /es/guide/introduction
    - theme: alt
      text: Ver en GitHub
      link: https://github.com/rivalex/clearance

features:
  - icon: 🖥️
    title: Panel de administración completo
    details: Permisos, Roles, Guardias, Configuración y un Panel de Control - usa las rutas prediseñadas o incorpora cada componente Livewire individualmente en cualquier parte de tu app.

  - icon: 🎯
    title: Autorización contextual
    details: "Otorga un rol o permiso limitado a una instancia de modelo específica - una Tienda, un Tenant, un Proyecto - con $user->canIn(), las directivas Blade @canin/@hasrolein, y un ContextService."

  - icon: 🏔️
    title: Roles techo
    details: Un rol puede declarar un rol padre cuyo conjunto de permisos actúa como límite superior. Un rol hijo nunca puede superar a su padre, algo que se aplica automáticamente en cada guardado.

  - icon: 🔑
    title: Permisos de escritura granulares
    details: Un permiso delegable clearance-{section}-write por cada sección del panel, para que puedas otorgar "gestionar roles" sin otorgar también "gestionar configuración".

  - icon: 👑
    title: Super Admin, opcional
    details: Un bypass de Gate global opcional, detección de alias para roles de tipo admin existentes, y promoción segura y no destructiva de un rol existente.

  - icon: 🗄️
    title: Nunca toca las tablas de Spatie
    details: Clearance es propietario de 6 tablas dedicadas clr_* y nunca altera roles, permissions, model_has_roles, model_has_permissions o role_has_permissions.

  - icon: 🛡️
    title: Reforzado en seguridad
    details: Techos de escalada de privilegios en cada ruta que otorga permisos, overrides de contexto con criterio de denegación autoritaria, un sanitizador de SVG basado en lista de permitidos, y un namespace clearance-* protegido.

  - icon: 👥
    title: Módulo de Usuarios (opcional)
    details: Asigna roles de forma global o a un contexto específico, además de overrides de permisos por contexto, desde un panel por usuario.

  - icon: 🌐
    title: 9 idiomas incluidos
    details: "ar, en, es, fr, hi, it, pt, ru, zh - con paridad de claves verificada en cada cambio."
---
