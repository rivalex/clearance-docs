# Guards

O painel de Guards gere guards de autenticação como registos de base de dados, para que os guards possam ser adicionados ou alterados sem editar `config/auth.php` e reimplementar.

## O que o painel faz

- **Criar / editar / eliminar** guards, cada um com um `name`, `driver`, e `provider`.

<Screenshot src="/screenshots/guards-page.png" alt="Painel de Guards do Clearance a listar guards apoiados em base de dados" caption="O painel de Guards lista todos os guards apoiados em base de dados com o seu driver e provider." />

## Como os guards são aplicados

Os guards geridos a partir do painel são injetados em `auth.guards` no arranque da aplicação, pelo que são reconhecidos pelo sistema de autenticação do Laravel antes de qualquer pedido ser executado - sem necessidade de edições manuais a `config/auth.php`.

## Drivers em lista de permissões

Apenas os drivers na lista de permissões são aceites:

```php
// config/clearance.php
'allowed_guard_drivers' => ['session', 'token', 'jwt', 'passport', 'sanctum'],
```

::: warning Drivers não listados são ignorados, não rejeitados ruidosamente
Um guard com um driver que não está em `allowed_guard_drivers` é ignorado no arranque (com um aviso no log) em vez de corromper a configuração de autenticação. Se um guard que criou não estiver a fazer efeito, verifique primeiro o driver contra a lista de permissões.
:::

## Controlo de acesso

O acesso de leitura requer `clearance-access`. Criar, editar, ou eliminar um guard requer `clearance-guards-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-guards-write');
```

## Rota e componente incorporado

| URI | Nome da rota | Descrição |
|---|---|---|
| `/clearance/guards` | `clearance.guards` | Gestão de Guards (criar/editar/eliminar) |

```blade
<livewire:clearance::guards.guard-manager />
```

## Base de dados

| Tabela | Colunas-chave |
|---|---|
| `clr_guards` | `name` (único), `driver`, `provider` |

## Próximos passos

- [Referência de configuração](/pt/guide/configuration) - as chaves completas de configuração `allowed_guard_drivers` e `guards`.
- [Referência do Facade](/pt/api/facade) - `Clearance::guards()` devolve os nomes de todos os guards de autenticação geridos.
