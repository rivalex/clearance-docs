# Serviços

Todos os serviços do Clearance residem em `src/Services/` e são resolvidos a partir do container, ex. `app(ContextService::class)`. São a camada a usar a partir de controllers, políticas, e jobs — em qualquer lugar onde o modelo `User` possa não usar a trait `HasClearance`.

## `ContextService`

`Rivalex\Clearance\Services\ContextService` (`src/Services/ContextService.php`) é o motor por trás da autorização contextual. O [facade `Clearance`](/pt/api/facade.md) e a trait `HasClearance` delegam ambos para ele.

```php
use Rivalex\Clearance\Services\ContextService;

$service = app(ContextService::class);

$service->canIn($user, 'orders-create', $store);
$service->hasPermissionIn($user, 'orders-create', $store); // alias
$service->hasRoleIn($user, 'manager', $store);
$service->resolveFor($user, $store); // Collection of all effective permission names
```

O `resolveFor()` constrói o seu resultado usando uma precedência de 3 fontes:

1. Concessões de papel contextuais
2. Concessões de papel globais
3. Substituições por contexto (`forced_on` acrescenta, `forced_off` ganha sempre)

Ver [Autorização Contextual](/pt/features/contextual-authorization.md) para a explicação conceptual desta precedência.

## `RoleService`

`Rivalex\Clearance\Services\RoleService` (`src/Services/RoleService.php`) gere papéis-teto.

```php
use Rivalex\Clearance\Services\RoleService;

app(RoleService::class)->setParent($childRole, $parentRole);
app(RoleService::class)->removeParent($childRole);
```

| Método | Descrição |
|---|---|
| `setParent($childRole, $parentRole)` | Define um teto: as permissões em excesso em `$childRole` além do conjunto do principal são silenciosamente cortadas. Lança `ClearanceScopeViolationException` se o filho já for um principal, ou o principal já for um filho. |
| `removeParent($childRole)` | Remove a relação de teto. |

::: warning
Um teto apenas corta — nunca concede. Um papel principal permissivo não dá, por si só, qualquer permissão ao filho. Remover uma permissão de um papel principal propaga essa remoção automaticamente a todos os papéis filho.
:::

Ver [Papéis](/pt/features/roles.md) para o conceito de papel-teto em maior profundidade.

## `PermissionService`

`Rivalex\Clearance\Services\PermissionService` (`src/Services/PermissionService.php`) suporta o CRUD do painel de Permissões.

Aplica a convenção de nomenclatura `group-action`, controlada pelas chaves de configuração `enforce_naming_convention` e `naming_separator`, e protege o namespace de permissões `clearance-*` de ser criado, renomeado, ou eliminado fora do fluxo de instalação.

## `GuardService`

`Rivalex\Clearance\Services\GuardService` (`src/Services/GuardService.php`) suporta o CRUD do painel de Guards.

Injeta guards apoiados em base de dados em `auth.guards` no arranque, filtrados pela chave de configuração `allowed_guard_drivers` — um driver não listado é ignorado com um aviso no log em vez de corromper a configuração de autenticação.

## `MetaService`

`Rivalex\Clearance\Services\MetaService` (`src/Services/MetaService.php`) lê e escreve `clr_meta` (nome de exibição, descrição, ícone SVG sanitizado, cor) para um papel ou guard sujeito. Suporta o editor de metadados de exibição do painel de Configurações.

Os ícones SVG passam por `Rivalex\Clearance\Support\SvgSanitizer` (um sanitizador com lista de permissões) tanto antes do armazenamento como no momento da renderização.

## `UserClearanceService`

`Rivalex\Clearance\Services\UserClearanceService` (`src/Services/UserClearanceService.php`) suporta o painel do módulo de Utilizadores:

- Atribuição de papel global
- Atribuição de papel contextual (via `UserRoleContext`)
- Substituições de permissão por contexto (via `UserContextPermissionOverride`, `forced_on` / `forced_off`)
- Remoção de atribuições, que propaga a eliminação de substituições

Aplica um teto de privilégio: um ator que não seja `super_admin` não pode modificar as suas próprias atribuições, nem pode conceder uma permissão que ele próprio não detenha. Ver [Super Admin](/pt/features/super-admin.md) e [Utilizadores](/pt/features/users.md).

## Exceções

Todas em `src/Exceptions/`:

| Exceção | Lançada quando |
|---|---|
| `ClearanceConfigException` | É lido um valor inválido ou mal configurado de `config/clearance.php` (ex. um `naming_separator` inválido). |
| `ClearanceNamingException` | Um nome de permissão viola a convenção de nomenclatura `group-action` enquanto a imposição está ativa. |
| `ClearanceProtectedResourceException` | É feita uma tentativa de modificar uma permissão `clearance-*` protegida, ou um recurso bloqueado, fora do fluxo de instalação. |
| `ClearanceScopeViolationException` | Uma relação de papel-teto inválida (um principal a tentar tornar-se filho, ou vice-versa), ou uma incompatibilidade de escopo. |

## Ver também

- [Facade](/pt/api/facade.md) — o wrapper de chamada estática em torno do `ContextService`.
- [Traits & Diretivas](/pt/api/traits-directives.md) — equivalentes de método de instância e diretiva Blade.
- [Comandos Artisan](/pt/api/artisan-commands.md) — `clearance:install` e `clearance:backfill`, que semeiam os dados sobre os quais estes serviços operam.
