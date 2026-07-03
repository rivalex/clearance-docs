# Configuração

O Clearance vem com valores padrão sensatos, pelo que funciona imediatamente após a [instalação](/pt/guide/installation). Publique o ficheiro de configuração quando precisar de personalizar o prefixo de rota, o middleware, os modelos contextuais, ou qualquer outra definição.

```bash
php artisan vendor:publish --tag=clearance-config
```

Isto publica `config/clearance.php`.

## Referência de configuração

Esta é a referência definitiva para cada chave em `config/clearance.php`. As restantes páginas desta documentação remetem para aqui em vez de repetir a tabela.

| Chave | Padrão | Finalidade |
|---|---|---|
| `route_prefix` | `'clearance'` | Prefixo de URI para todas as rotas do painel. |
| `middleware` | `['web', 'auth']` | Aplicado a todas as rotas do painel antes de `clearance.access`. |
| `access_permission` | `'clearance-access'` | Permissão verificada pelo middleware de acesso. |
| `user_model` | `null` | Assume por padrão `auth.providers.users.model`. |
| `modules.users` | `false` | Interruptor principal do módulo de Utilizadores (rotas + componentes Livewire). |
| `contextual_models` | `[]` | `FQCN => ['label', 'icon', 'label_attribute', 'searchable']` - modelos que podem atuar como contextos de vinculação de papéis. |
| `enforce_naming_convention` | `true` | Aplica a convenção de nomenclatura `group-action` para permissões. |
| `naming_separator` | `'-'` | Carácter separador - apenas `'-'` ou `'_'`. |
| `guards` | `[]` | Substitui os guards auto-detetados a partir de `config/auth.php`. Vazio = auto-detetar todos. |
| `allowed_guard_drivers` | `['session', 'token', 'jwt', 'passport', 'sanctum']` | Drivers aceites ao injetar guards de base de dados em `auth.guards`. |
| `layout` | `null` | Layout Blade para componentes de página completa. `null` = padrão da aplicação hospedeira (`config('livewire.layout')`). |
| `auto_assign_default_role` | `false` | Atribui automaticamente o papel padrão configurado nas Configurações no evento `Registered`. |
| `super_admin_gate_bypass` | `false` | Opcional: `super_admin` contorna todas as verificações de Gate em toda a aplicação. |
| `ui.flux_pro` | `null` | `null` = auto-deteta via `Flux::pro()`. |

## Convenção de nomenclatura de permissões

As permissões devem seguir o formato `group-action`:

```
orders-create       correct
orders-read         correct
store-orders-delete correct

create              wrong (bare action, no group)
orders.create       wrong (dot separator)
Orders-Create       wrong (camelCase)
```

Desative a imposição por ambiente com `enforce_naming_convention`:

```php
'enforce_naming_convention' => false,
```

::: warning O separador é validado no arranque
O valor de `naming_separator` é a única peça desta configuração que chega a um fragmento SQL bruto (agrupamento/ordenação de permissões), pelo que apenas `-` ou `_` são aceites.
:::

## Personalizar o middleware

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

Os nomes de rota mantêm-se estáveis independentemente de alterações ao prefixo ou ao middleware - ver o [Início Rápido](/pt/guide/quick-start) para a tabela completa de rotas.

## Relacionado

- [Início Rápido](/pt/guide/quick-start) - montar o painel com estas definições aplicadas
- [Publicação de recursos](/pt/guide/publishing) - publicar traduções e compreender a chave `layout`
- [Guards](/pt/features/guards) - como `guards` e `allowed_guard_drivers` são usados
- [Super Admin](/pt/features/super-admin) - como funciona `super_admin_gate_bypass`
