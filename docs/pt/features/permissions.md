# Permissões

O painel de Permissões é uma interface CRUD completa sobre o modelo `Permission` do Spatie. O Clearance nunca introduz a sua própria tabela de permissões - toda a permissão que cria aqui é uma permissão Spatie normal, utilizável em qualquer lugar onde a API do Spatie seja usada (`$user->can()`, `Gate::allows()`, políticas, `@can`).

## O que o painel faz

- **Criar** - adicionar uma nova permissão, validada contra a [convenção de nomenclatura](#convenção-de-nomenclatura-de-permissões) (a menos que desativada) e delimitada a um guard.
- **Editar** - renomear uma permissão ou alterar o seu guard.
- **Eliminar** - remover uma permissão e as suas atribuições de papel/utilizador (comportamento de cascade padrão do Spatie).

<Screenshot src="/screenshots/permissions-page.png" alt="Painel de Permissões do Clearance a listar permissões agrupadas por prefixo de nomenclatura" caption="O painel de Permissões lista todas as permissões do Spatie, agrupadas por prefixo de nomenclatura." />

<Screenshot src="/screenshots/permission-form-modal.png" alt="Formulário de criação/edição de permissão com validação da convenção de nomenclatura" caption="O formulário de permissão valida a convenção de nomenclatura group-action antes de gravar." />

## Convenção de Nomenclatura de Permissões

As permissões devem seguir o formato `group-action`:

```
orders-create       correct
orders-read         correct
store-orders-delete correct

create              wrong (bare action, no group)
orders.create       wrong (dot separator)
Orders-Create       wrong (camelCase)
```

O prefixo do grupo (`orders` em `orders-create`) é o que o [Painel de Controle](/pt/features/dashboard) usa para contar grupos de permissões distintos.

::: tip Desativar a imposição
Algumas equipas migram de um esquema de nomenclatura diferente. Desative a verificação por ambiente:

```php
// config/clearance.php
'enforce_naming_convention' => false,
```
:::

## Controlo de acesso

O acesso de leitura ao painel (ver a lista de permissões) requer `clearance-access`. Criar, editar, ou eliminar uma permissão requer adicionalmente `clearance-permissions-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-permissions-write');
```

::: warning As permissões `clearance-*` são reservadas
As próprias permissões `clearance-*` (`clearance-access`, `clearance-permissions-write`, etc.) não podem ser criadas, renomeadas, ou eliminadas através deste painel - mesmo por um ator `super_admin`. São geridas exclusivamente pelo próprio fluxo de instalação do pacote. Ver [Super Admin](/pt/features/super-admin#permissões-clearance-reservadas).
:::

## Rota e componente incorporado

| URI | Nome da rota | Descrição |
|---|---|---|
| `/clearance/permissions` | `clearance.permissions` | CRUD de Permissões com validação de nomenclatura por prefixo de grupo |

```blade
<livewire:clearance::permissions.permission-manager />
```

## Próximos passos

- [Papéis](/pt/features/roles) - atribuir permissões a um papel, delimitado ao guard.
- [Super Admin](/pt/features/super-admin) - a tabela completa de permissões de escrita granulares.
- [Referência de configuração](/pt/guide/configuration) - `enforce_naming_convention`, `naming_separator`.
