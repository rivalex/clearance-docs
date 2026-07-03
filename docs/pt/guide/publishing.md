# Publicação

O Clearance publica três tipos de recursos via tags `vendor:publish`: configuração, migrações, e traduções. A configuração e as migrações são geridas automaticamente pelo instalador (ver [Instalação](/pt/guide/installation)); esta página cobre a publicação manual destes, e a publicação de traduções.

## Configuração

```bash
php artisan vendor:publish --tag=clearance-config
```

Publica `config/clearance.php` na sua aplicação. Ver a [referência de configuração](/pt/guide/configuration) para todas as chaves, o seu padrão, e a sua finalidade.

## Traduções

```bash
php artisan vendor:publish --tag=clearance-translations
```

Isto copia os 9 idiomas incluídos (`ar`, `en`, `es`, `fr`, `hi`, `it`, `pt`, `ru`, `zh`) para `lang/vendor/clearance/{locale}/ui.php` na sua aplicação. Para adicionar um novo idioma ou substituir strings num já existente, adicione um diretório de idioma ali, usando `en/ui.php` como referência canónica de chaves. Todos os idiomas expõem um conjunto de chaves idêntico, verificado por um teste dedicado a cada alteração.

## Substituir o layout do painel

A chave de configuração `layout` controla o layout Blade usado pelos componentes de página completa:

```php
// config/clearance.php
'layout' => null, // host app's default (config('livewire.layout'))
```

Deixe-a como `null` para usar o layout Livewire padrão da sua aplicação, ou defina-a para uma view de layout específica para renderizar o painel dentro de uma estrutura personalizada.

## Migrações

As migrações do Clearance são publicadas e executadas automaticamente por `php artisan clearance:install` - o instalador também publica e executa primeiro as migrações do Spatie se a tabela `roles` ainda não existir. Ver [Instalação](/pt/guide/installation) para o fluxo completo passo a passo, e o [aviso de perigo](/pt/guide/installation#executar-o-instalador) sobre nunca executar um `migrate` simples numa instalação nova.

## Relacionado

- [Referência de configuração](/pt/guide/configuration) - todas as chaves de configuração
- [Instalação](/pt/guide/installation) - o fluxo completo do instalador
- [Migração](/pt/guide/migration) - `clearance:backfill` para instalações Spatie existentes
