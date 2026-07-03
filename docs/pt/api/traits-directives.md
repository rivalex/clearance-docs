# Traits & Diretivas

Referência para as traits, concern, contrato, e diretivas Blade que compõem a superfície de desenvolvimento do Clearance fora do facade e dos serviços.

## `HasClearance`

`Rivalex\Clearance\Traits\HasClearance` — adicione ao seu modelo `User`.

```php
use Rivalex\Clearance\Traits\HasClearance;

class User extends Authenticatable
{
    use HasClearance;
}
```

Inclui internamente a `HasRoles` do Spatie, pelo que esta única trait substitui ambas — não adicione `Spatie\Permission\Traits\HasRoles` separadamente.

Acrescenta os seguintes métodos de instância:

```php
$user->canIn('orders-create', $store);            // bool
$user->canIn('orders-create', $store, 'api');     // optional guard filter
$user->hasPermissionIn('orders-create', $store);  // alias of canIn()
$user->hasRoleIn('manager', $store);               // bool
$user->hasRoleIn('manager', $store, 'web');         // optional guard filter
```

| Método | Devolve | Descrição |
|---|---|---|
| `canIn(string $permission, $context, ?string $guard = null)` | `bool` | Verificação de permissão delimitada a uma instância de contexto. |
| `hasPermissionIn(string $permission, $context, ?string $guard = null)` | `bool` | Alias de `canIn()`. |
| `hasRoleIn(string $role, $context, ?string $guard = null)` | `bool` | Verificação de papel delimitada a uma instância de contexto. |

::: tip
Sem esta trait, estes métodos de instância ficam indisponíveis — mas as diretivas Blade e o `ContextService` continuam a funcionar de qualquer forma. Nenhum deles depende da trait.
:::

## `HasPermissionGroups`

`Rivalex\Clearance\Concerns\HasPermissionGroups` — use quando precisar de um modelo `Permission` personalizado (configurado via `config('permission.models.permission')`) em vez do próprio `Rivalex\Clearance\Models\Permission` do Clearance.

Aplique-a para manter os acessores de UI agrupada que suportam o painel de Permissões a funcionar no seu modelo personalizado:

```php
use Rivalex\Clearance\Concerns\HasPermissionGroups;

class Permission extends \Spatie\Permission\Models\Permission
{
    use HasPermissionGroups;
}
```

Fornece os seguintes acessores:

| Acessor / método | Descrição |
|---|---|
| `permission_group` | O prefixo do grupo antes do separador de nomenclatura (ex. `orders` em `orders-create`). |
| `group_string` | O grupo como string de exibição. |
| `abilities()` | O conjunto de habilidades/ações definidas para o grupo da permissão. |
| `colorForAbility()` | Uma cor para a habilidade da permissão, usada pela UI do painel. |

Ver [Permissões](/pt/features/permissions.md) para saber como a convenção de nomenclatura e o agrupamento são usados no painel.

## `ClearanceContextable`

`Rivalex\Clearance\Contracts\ClearanceContextable` — implemente num modelo de contexto (ex. o seu modelo `Store`) para controlar como é rotulado no painel.

```php
interface ClearanceContextable
{
    public function clearanceLabel(): string;
}
```

Se um modelo de contexto não implementar este contrato, o painel recorre a `contextual_models.<FQCN>.label_attribute` (padrão `name`) de `config/clearance.php`. Ver [Configuração](/pt/guide/configuration.md).

## Diretivas Blade

```blade
@canin('orders-create', $store)
    <button>New Order</button>
@endcanin

@hasrolein('manager', $store)
    <a href="{{ route('reports') }}">Reports</a>
@endhasrolein
```

| Diretiva | Verificação equivalente |
|---|---|
| `@canin($permission, $context)` | `ContextService::canIn()` |
| `@hasrolein($role, $context)` | `ContextService::hasRoleIn()` |

::: tip
Ambas as diretivas resolvem-se de forma segura para um utilizador convidado — devolvem `false` / saltam o bloco e nunca lançam uma exceção. Também não requerem a trait `HasClearance`; resolvem-se através do mesmo `ContextService` subjacente usado pelo facade.
:::

## Ver também

- [Facade](/pt/api/facade.md) — o equivalente de chamada estática destes métodos de instância.
- [Serviços](/pt/api/services.md) — internos do `ContextService` e a precedência de resolução das 3 fontes.
