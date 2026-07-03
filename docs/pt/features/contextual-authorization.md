# Autorização Contextual

O Clearance estende o modelo de autorização global do Spatie com verificações delimitadas por contexto. Os papéis de um utilizador podem ser atribuídos dentro de um contexto específico (ex. uma Store, Tenant, ou Project) e são invisíveis fora desse escopo. Este é o mecanismo central por trás do [módulo de Utilizadores](/pt/features/users) e dos [Papéis](/pt/features/roles#escopo-global-vs-contextual) contextuais.

## No modelo User

Disponível quando o seu modelo `User` usa a trait `HasClearance` (ver [Instalação](/pt/guide/installation#adicionar-o-hasclearance-ao-seu-modelo-user)):

```php
// Does the user have this permission in this context?
$user->canIn('orders-create', $store);

// Alias - mirrors Spatie's hasPermissionTo() naming
$user->hasPermissionIn('orders-create', $store);

// Does the user hold this role in this context?
$user->hasRoleIn('manager', $store);

// Optionally filter by guard
$user->canIn('orders-create', $store, 'api');
$user->hasRoleIn('manager', $store, 'web');
```

## Em views Blade

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

::: tip Seguro para convidados por conceção
Ambas as diretivas resolvem-se de forma segura (devolvem `false`) para um utilizador convidado - nunca lançam uma exceção. Pode usar `@canin`/`@hasrolein` em views renderizadas antes de a autenticação estar garantida, sem as envolver numa verificação `@auth`.
:::

## Via ContextService

Para uso em controllers, políticas, ou jobs onde o modelo `User` pode não ter a trait `HasClearance`:

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

## Via o facade Clearance

O facade `Clearance` delega para o mesmo serviço - um ponto de entrada global conveniente:

```php
Clearance::canIn($user, 'orders-create', $store);
Clearance::resolveFor($user, $store);
Clearance::guards(); // names of all managed authentication guards
```

Ver a [referência do Facade](/pt/api/facade) para a lista completa de métodos.

## Como funciona a resolução

O `resolveFor()` combina três fontes, por esta ordem de precedência:

1. **Concessões de papel contextuais** - permissões de papéis que o utilizador detém especificamente dentro deste contexto (`clr_role_ctx`).
2. **Concessões de papel globais** - permissões dos papéis do Spatie atribuídos globalmente ao utilizador (para que `super_admin` e outros papéis globais satisfaçam `canIn()` para qualquer contexto).
3. **Substituições por contexto** (`clr_ctx_overrides`) - `forced_on` acrescenta uma permissão às anteriores; `forced_off` remove-a, mesmo que a permissão tenha sido concedida por um papel global.

::: danger Uma negação ganha sempre
Se uma permissão for `forced_off` para um utilizador num determinado contexto, essa negação sobrepõe-se a todas as outras fontes - incluindo uma concessão de papel global e uma concessão de papel contextual. Não há forma de reconceder uma permissão `forced_off` para esse contexto, exceto removendo a própria substituição. Desenhe a sua política de substituições tendo isto em mente: `forced_off` é o sinal mais forte do sistema.
:::

## Base de dados

| Tabela | Colunas-chave |
|---|---|
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - único por `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) |

## Próximos passos

- [Utilizadores](/pt/features/users) - a UI do painel para atribuir papéis e substituições contextuais.
- [Papéis](/pt/features/roles#escopo-global-vs-contextual) - declarar um papel como `contextual` e os seus `context_types` permitidos.
- [Referência de Traits e Diretivas](/pt/api/traits-directives) - assinaturas completas da trait `HasClearance` e das diretivas Blade.
- [Referência de serviços](/pt/api/services) - API completa do `ContextService`.
