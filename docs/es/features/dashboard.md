# Panel de Control

El Panel de Control es la página de inicio del panel de Clearance. Ofrece un resumen de un vistazo del estado de autorización de tu aplicación - sin necesidad de configuración.

## Qué muestra

- **Total de roles** - recuento de todos los roles de Spatie.
- **Total de guards** - recuento de todos los guards gestionados por Clearance (consulta [Guards](/es/features/guards)).
- **Total de permisos** - recuento de todos los permisos de Spatie.
- **Grupos de permisos** - el número de grupos de permisos distintos, derivado del prefijo de nomenclatura (el `group` en `group-action`, p. ej. `orders` en `orders-create`). Consulta [Permisos](/es/features/permissions) para la convención de nomenclatura.
- **Asignaciones de rol contextuales** - el número de asignaciones de rol limitadas a una instancia de modelo específica (una Tienda, un Tenant, un Proyecto). Consulta [Autorización Contextual](/es/features/contextual-authorization).
- **Top 5 roles por número de usuarios** - los cinco roles con más usuarios asignados, ordenados.

<Screenshot src="/screenshots/dashboard-page.png" alt="Panel de Control de Clearance mostrando el recuento de roles, guards y permisos" caption="El Panel de Control resume roles, guards, permisos, grupos, asignaciones contextuales y los roles principales." />

## Acceder al Panel de Control

| URI | Nombre de ruta | Descripción |
|---|---|---|
| `/clearance` | `clearance.home` | Panel de Control |

Leer el Panel de Control requiere el permiso `clearance-access`, igual que cualquier otra sección del panel. Consulta [Super Admin](/es/features/super-admin#panel-access-fine-grained-write-permissions) para la tabla completa de control de acceso.

## Incorporar el componente

El Panel de Control es un componente Livewire autocontenido. Móntalo en cualquier parte de tus propios layouts en lugar de - o además de - la ruta prediseñada `/clearance`:

```blade
<livewire:clearance::dashboard />
```

## Próximos pasos

- [Permisos](/es/features/permissions) - gestiona la lista de permisos que alimenta los recuentos del Panel de Control.
- [Roles](/es/features/roles) - gestiona los roles detrás de la estadística "top 5 por número de usuarios".
- [Autorización Contextual](/es/features/contextual-authorization) - qué es una asignación de rol contextual.
