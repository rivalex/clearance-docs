# Publicación

Clearance publica tres tipos de assets vía `vendor:publish`: configuración, migraciones y traducciones. La configuración y las migraciones se gestionan automáticamente mediante el instalador (consulta [Instalación](/es/guide/installation)); esta página cubre cómo publicarlos manualmente, y cómo publicar las traducciones.

## Configuración

```bash
php artisan vendor:publish --tag=clearance-config
```

Publica `config/clearance.php` en tu aplicación. Consulta la [referencia de configuración](/es/guide/configuration) para cada clave, su valor predeterminado y su propósito.

## Traducciones

```bash
php artisan vendor:publish --tag=clearance-translations
```

Esto copia los 9 idiomas incluidos (`ar`, `en`, `es`, `fr`, `hi`, `it`, `pt`, `ru`, `zh`) a `lang/vendor/clearance/{locale}/ui.php` en tu app. Para añadir un nuevo idioma o sobrescribir cadenas en uno existente, añade un directorio de idioma ahí, usando `en/ui.php` como referencia canónica de claves. Todos los idiomas exponen un conjunto de claves idéntico, verificado mediante una prueba dedicada en cada cambio.

## Sobrescribir el layout del panel

La clave de configuración `layout` controla el layout Blade usado por los componentes de página completa:

```php
// config/clearance.php
'layout' => null, // host app's default (config('livewire.layout'))
```

Déjala en `null` para usar el layout Livewire predeterminado de tu aplicación, o establécela en una vista de layout específica para renderizar el panel dentro de una estructura personalizada.

## Migraciones

Las migraciones de Clearance se publican y ejecutan automáticamente mediante `php artisan clearance:install` - el instalador también publica y ejecuta primero las migraciones de Spatie si la tabla `roles` aún no existe. Consulta [Instalación](/es/guide/installation) para el flujo completo paso a paso, y el [aviso de peligro](/es/guide/installation#run-the-installer) sobre nunca ejecutar un `migrate` simple en una instalación nueva.

## Relacionado

- [Referencia de configuración](/es/guide/configuration) - cada clave de configuración
- [Instalación](/es/guide/installation) - el flujo completo del instalador
- [Migración](/es/guide/migration) - `clearance:backfill` para instalaciones de Spatie existentes
