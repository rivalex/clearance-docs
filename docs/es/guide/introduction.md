# Introducción

## ¿Qué es Clearance?

Clearance es un panel de administración listo para usar en aplicaciones Laravel, construido sobre [spatie/laravel-permission](https://github.com/spatie/laravel-permission). Añade un panel Livewire 4 + Flux UI para gestionar permisos, roles, guards y configuración, además de una capa de autorización contextual y techos de rol - instalable con un solo comando artisan, sin alterar ninguna tabla de Spatie.

Clearance te ofrece:

- **Panel de administración** para Permisos, Roles, Guards, Configuración y un Panel de Control - usa las rutas prediseñadas o incorpora cada componente Livewire individualmente.
- **Autorización contextual** - otorga un rol o permiso limitado a una instancia de modelo específica (una Tienda, un Tenant, un Proyecto), con `$user->canIn()`, las directivas Blade `@canin`/`@hasrolein`, y un `ContextService` para usar fuera del modelo User.
- **Roles techo** - un rol puede declarar un rol padre cuyo conjunto de permisos actúa como límite superior; un rol hijo nunca puede superar a su padre, algo que se aplica automáticamente en cada guardado.
- **Permisos de escritura granulares** - un permiso delegable `clearance-{section}-write` por cada sección del panel, para que puedas otorgar "gestionar roles" sin otorgar también "gestionar configuración".
- **Super Admin** con un bypass de Gate global opcional, detección de alias para roles de tipo admin existentes, y promoción segura no destructiva.
- **Módulo de Usuarios** (opcional) - asigna roles de forma global o a un contexto específico, además de overrides de permisos por contexto, desde un panel por usuario.
- **Panel de Configuración** - nombre de visualización, descripción, icono SVG sanitizado y color por rol/guard; un rol predeterminado configurable con auto-asignación y asignación masiva.
- **Gestión de Guards** - guards respaldados por base de datos, inyectados en `auth.guards` al arrancar, restringidos a un conjunto de drivers en lista de permitidos.
- **9 idiomas incluidos** (ar, en, es, fr, hi, it, pt, ru, zh), con paridad de claves verificada.
- **Puntos de extensión** - el trait `HasClearance`, el trait `HasPermissionGroups`, el contrato `ClearanceContextable`, componentes Blade reutilizables.
- **Sanitización de SVG** - cada icono proporcionado por el usuario pasa por un sanitizador basado en lista de permitidos antes de almacenarse y también al renderizarse.
- **Reforzado en seguridad** - techos de escalada de privilegios en cada ruta que otorga permisos, overrides de contexto con criterio de denegación autoritaria, namespace `clearance-*` protegido. Consulta [Seguridad](/es/guide/security).

## Relación con spatie/laravel-permission

Clearance no reemplaza al paquete de permisos de Spatie - es una capa de UI y autorización sobre él. Los roles y permisos siguen siendo los modelos `Role` y `Permission` de Spatie, almacenados en las tablas `roles`, `permissions`, `model_has_roles`, `model_has_permissions` y `role_has_permissions` de Spatie. Clearance nunca altera esas tablas. Todo lo que Clearance añade - metadatos de visualización, alcance de rol, techos, asignaciones contextuales, overrides por contexto - vive en su propio conjunto de tablas `clr_*`. Consulta [Arquitectura](/es/guide/architecture) para el desglose completo.

## Requisitos

| Dependencia | Versión |
|---|---|
| PHP | ^8.3 |
| Laravel | ^11.0 \| ^12.0 \| ^13.0 |
| spatie/laravel-permission | ^6.0 |
| livewire/livewire | ^3.0 \| ^4.0 |
| livewire/flux | ^2.14 |

## Conceptos clave

| Concepto | Resumen |
|---|---|
| Permission | Un permiso de Spatie que sigue la convención de nomenclatura `group-action` (p. ej. `orders-create`). Otorga una capacidad. |
| Alcance de rol | Todo rol es `global` (asignado directamente, se aplica en todas partes) o `contextual` (vinculado a tipos de modelo específicos, asignable solo dentro de una instancia de contexto). |
| Rol techo | Un rol puede declarar un rol padre cuyo conjunto de permisos es un límite superior - el hijo nunca puede superarlo. |
| Contexto | Una instancia de modelo (una Tienda, un Tenant, un Proyecto) a la que puede limitarse un rol o un override de permiso. |
| Override | Una concesión o denegación de permiso `forced_on`/`forced_off` por usuario y por contexto - una denegación siempre gana. |

Cada uno de estos conceptos tiene una página completa: [Permisos](/es/features/permissions), [Roles](/es/features/roles), [Autorización Contextual](/es/features/contextual-authorization), [Usuarios](/es/features/users), [Super Admin](/es/features/super-admin).

## Próximos pasos

¿Listo para instalar Clearance? Continúa con la [guía de Instalación](/es/guide/installation).
