# Instalação

## Requisitos

Ver a [tabela de requisitos](/pt/guide/introduction#requisitos) completa na página de Introdução - PHP ^8.3, Laravel ^11.0|^12.0|^13.0, spatie/laravel-permission ^6.0, livewire/livewire ^3.0|^4.0, livewire/flux ^2.14.

## Instalar o pacote

```bash
composer require rivalex/clearance
```

## Executar o instalador

```bash
php artisan clearance:install
```

::: danger Execute sempre clearance:install, nunca um migrate simples
As próprias migrações do Clearance dependem da existência prévia das tabelas `roles`/`permissions` do Spatie; o instalador publica e executa as migrações do Spatie antes das suas próprias. Se executar `php artisan migrate` diretamente numa instalação nova e o Spatie ainda não estiver configurado, receberá uma mensagem de erro acionável a indicar que deve executar o instalador em vez disso.
:::

### Flags opcionais

| Flag | Descrição |
|---|---|
| `--user=ID` | Atribui o papel `super_admin` a um utilizador após a instalação |
| `--role=NAME` | Atribui `clearance-access` a um papel (criado se não existir) |
| `--super-admin-role=NAME` | Promove um papel existente a `super_admin` (salta o prompt interativo) |
| `--force` | Executa novamente mesmo que já esteja instalado |

### O que o instalador faz

1. Publica `config/clearance.php`.
2. Publica as migrações do Clearance.
3. Deteta as tabelas de Permissões do Spatie - se a tabela `roles` não existir, publica e executa primeiro as migrações do Spatie automaticamente.
4. Executa todas as migrações pendentes.
5. Cria `clearance-access` e todas as 5 permissões de capacidade granulares: `clearance-permissions-write`, `clearance-roles-write`, `clearance-guards-write`, `clearance-settings-write`, `clearance-users-write`.
6. Cria o papel `super_admin` com todas as permissões `clearance-*` atribuídas (aditivo - nunca remove permissões existentes ao executar novamente).
7. Escreve `storage/.clearance-installed` como marcador de idempotência.

::: tip A migrar a partir de uma instalação Spatie existente?
Use `php artisan clearance:backfill` em vez de começar do zero, e leia primeiro o [guia de Migração](/pt/guide/migration) - cobre a colisão com `super_admin`, subclasses de modelo personalizadas, e a importação de guards.
:::

## Adicionar o HasClearance ao seu modelo User

Para usar os métodos de autorização contextual (`$user->canIn()`, `$user->hasRoleIn()`), adicione a trait `HasClearance` ao `App\Models\User`. Já inclui a `HasRoles` do Spatie - uma linha substitui ambas:

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance; // includes HasRoles + all contextual authorization methods
}
```

::: tip Já usa `use HasRoles`?
Substitua-a por `use HasClearance`, ou mantenha ambas - o PHP deduplica automaticamente a composição de traits, sem conflitos.
:::

Sem esta trait, `$user->canIn()` e métodos relacionados ficam indisponíveis, mas as diretivas Blade (`@canin`, `@hasrolein`) e o `ContextService` continuam a funcionar de qualquer forma - não dependem da trait. Ver [Autorização Contextual](/pt/features/contextual-authorization) para mais detalhes.

## Próximos passos

Continue para o [Início Rápido](/pt/guide/quick-start) para montar o painel e começar a gerir permissões.
