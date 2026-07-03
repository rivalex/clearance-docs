# Utilizadores

O módulo de Utilizadores é um painel por utilizador para atribuir papéis - globalmente ou delimitados a um contexto específico - e para conceder ou negar permissões individuais dentro de um contexto. É opcional e está desativado por padrão.

## Ativar o módulo

```php
// config/clearance.php
'modules' => ['users' => true],
```

## O que o painel faz

- **Atribuição de papel global** - atribuir papéis `scope=global` diretamente ao utilizador, em toda a aplicação.
- **Atribuição de papel contextual** - atribuir papéis `scope=contextual` delimitados a uma instância de modelo específica (uma Store, Team, ou Project declarada em `contextual_models`).
- **Substituições de permissão por contexto** - para cada atribuição de papel contextual, permissões adicionais podem ser forçadas a conceder ou forçadas a negar de forma independente por instância de contexto.
- **Remover atribuição** - remove um papel do utilizador (escopo global ou contextual), propagando a eliminação de quaisquer substituições associadas.

<Screenshot src="/screenshots/user-panel-global.png" alt="Painel de utilizador a mostrar a atribuição de papel global" caption="Atribuir um papel global a um utilizador - aplica-se em toda a aplicação." />

<Screenshot src="/screenshots/user-panel-contextual.png" alt="Painel de utilizador a mostrar a atribuição de papel contextual e substituições de permissão por contexto" caption="Atribuir um papel contextual e definir substituições de permissão por contexto para uma instância de Store específica." />

::: warning Teto de privilégio na auto-modificação
Tanto a atribuição de papéis como as concessões de permissão diretas/de substituição aplicam um teto de privilégio: um ator que não seja `super_admin` não pode modificar as suas próprias atribuições, nem pode conceder uma permissão que ele próprio não detenha.
:::

## Controlo de acesso

O acesso a `/clearance/user/{userId}` requer `clearance-access` (leitura) e `clearance-users-write` (atribuir/remover):

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-users-write');
```

## Rota e componente incorporado

| URI | Nome da rota | Descrição |
|---|---|---|
| `/clearance/user/{userId}` | `clearance.user` | Atribuição de papéis por utilizador + substituições de permissão contextuais (requer `modules.users = true`) |

```blade
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

### Ligar ao painel a partir da sua aplicação

```php
route('clearance.user', ['userId' => $user->id])
```

## Papéis contextuais nos bastidores

Atribuir um papel contextual a um utilizador cria uma linha `UserRoleContext`:

```php
use Rivalex\Clearance\Models\UserRoleContext;

// Samuele is "StoreStaff" only within Alessandro's Store
UserRoleContext::create([
    'user_id'      => $samuele->id,
    'role_id'      => $staffRole->id,
    'context_type' => Store::class,
    'context_id'   => $store->id,
]);
```

As verificações de autorização respeitam o contexto via `$user->canIn()`, `@canin`, e `ContextService`. Ver [Autorização Contextual](/pt/features/contextual-authorization) para o modelo de resolução completo.

## Base de dados

| Tabela | Colunas-chave |
|---|---|
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - único por `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) |

## Próximos passos

- [Autorização Contextual](/pt/features/contextual-authorization) - a precedência de resolução completa para `canIn()`.
- [Papéis](/pt/features/roles) - configurar o escopo de um papel e `context_types`.
- [Referência de configuração](/pt/guide/configuration) - `modules.users`, `contextual_models`.
