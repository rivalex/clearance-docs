# Papéis

O painel de Papéis é uma interface CRUD completa sobre o modelo `Role` do Spatie, estendida com dois conceitos específicos do Clearance: **escopo** (global vs. contextual) e **teto** (um papel principal que limita as permissões de um papel filho).

## O que o painel faz

- **Criar / editar / eliminar** papéis.
- **Atribuição de permissões delimitada ao guard** - atribuir permissões a um papel, filtradas pelo guard do papel.
- **Seleção de escopo** - `global` ou `contextual`.
- **Configuração de teto** - definir ou remover um papel principal.

<Screenshot src="/screenshots/roles-page.png" alt="Painel de Papéis do Clearance a listar papéis com escopo e teto" caption="O painel de Papéis lista todos os papéis com o seu escopo e, se definido, o seu papel-teto principal." />

## Escopo: global vs. contextual

Todo o papel declara um escopo no momento da criação:

- **`global`** - atribuído diretamente a um utilizador via `$user->assignRole()`, aplica-se em todo o lado.
- **`contextual`** - vinculado a um ou mais tipos de modelo (`context_types`), apenas atribuível dentro de uma instância de contexto específica via `UserRoleContext`. Ver [Autorização Contextual](/pt/features/contextual-authorization) e [Utilizadores](/pt/features/users#papéis-contextuais-nos-bastidores).

Os papéis sem uma linha `RoleMeta` explícita assumem `global` por padrão, para compatibilidade retroativa.

## Papéis-teto

Um papel pode declarar um teto - um papel principal cujo conjunto de permissões atua como limite superior:

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
```

<Screenshot src="/screenshots/role-form-ceiling.png" alt="Formulário de papel com um papel-teto principal selecionado" caption="Definir um papel-teto principal no formulário de Papel (RoleForm)." />

Regras aplicadas automaticamente a cada gravação:

- O papel filho só pode deter as permissões que o papel principal também detém. Qualquer excesso é silenciosamente cortado em `setParent()` e em toda a sincronização de permissões subsequente - sem exceção, sem mensagem de erro, apenas aplicado silenciosamente.
- Um papel que já atua como principal não pode tornar-se filho (`ClearanceScopeViolationException`).
- Um papel que já é filho não pode ele próprio atuar como principal (`ClearanceScopeViolationException`).
- Remover permissões de um papel principal propaga a remoção automaticamente a todos os papéis filho.

::: warning Um teto apenas corta, nunca concede
Definir um papel principal permissivo não concede nada ao filho por si só. O teto é estritamente um limite superior - nunca acrescenta permissões ao filho, apenas as pode remover.
:::

Isto é configurado por papel no painel de Papéis (`RoleForm`), ou programaticamente via `RoleService::setParent()` / `RoleService::removeParent()`.

## Controlo de acesso

O acesso de leitura requer `clearance-access`. Criar, editar, ou eliminar um papel - incluindo as suas atribuições de permissão, escopo, e teto - requer `clearance-roles-write`:

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

## Rota e componente incorporado

| URI | Nome da rota | Descrição |
|---|---|---|
| `/clearance/roles` | `clearance.roles` | CRUD de Papéis com atribuição de permissões delimitada ao guard, escopo (global/contextual), e teto |

```blade
<livewire:clearance::roles.role-manager />
```

## Base de dados

Os metadados de papel (escopo, teto, estado de bloqueio) residem em `clr_role_meta`, nunca na tabela `roles` do Spatie:

| Tabela | Colunas-chave |
|---|---|
| `clr_role_meta` | `role_id` (único, FK -> `roles`) - `is_locked`, `scope` (`global`\|`contextual`), `context_types` (json) - `parent_role_id` (FK anulável -> `roles`, `ON DELETE SET NULL`) |

## Próximos passos

- [Autorização Contextual](/pt/features/contextual-authorization) - como os papéis `scope=contextual` são verificados em tempo de execução.
- [Utilizadores](/pt/features/users) - atribuir papéis a um utilizador específico, globalmente ou por contexto.
- [Referência de serviços](/pt/api/services) - API completa do `RoleService`.
