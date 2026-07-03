# Facade

O facade `Clearance` (`Rivalex\Clearance\Facades\Clearance`) expõe a API de autorização contextual do pacote como chamadas de estilo estático. É apoiado por `src/Clearance.php`, que delega todas as chamadas ao [`ContextService`](/pt/api/services.md#contextservice).

```php
use Rivalex\Clearance\Facades\Clearance;
```

::: tip
As diretivas Blade e o `ContextService` funcionam sem qualquer alteração ao seu modelo `User`. O facade é um wrapper de conveniência em torno do mesmo motor — ver [Autorização Contextual](/pt/features/contextual-authorization.md) para o conceito por trás das verificações delimitadas por contexto.
:::

## `canIn()`

Verifica se um utilizador detém uma permissão delimitada a uma instância de contexto.

```php
Clearance::canIn($user, 'orders-create', $store);      // bool
Clearance::canIn($user, 'orders-create', $store, 'api'); // optional 3rd arg: guard name filter
```

| Parâmetro | Tipo | Descrição |
|---|---|---|
| `$user` | `Authenticatable` | O utilizador a verificar. |
| `string $permission` | `string` | Nome da permissão, ex. `orders-create`. |
| `$context` | instância de modelo | O contexto ao qual a permissão está delimitada, ex. uma `Store`. |
| `?string $guard` | `string\|null` | Filtro opcional de nome de guard. |

Devolve `bool`.

## `hasPermissionIn()`

Alias de [`canIn()`](#canin), nomeado para espelhar a convenção `hasPermissionTo()` do Spatie.

```php
Clearance::hasPermissionIn($user, 'orders-create', $store);
```

Devolve `bool`.

## `hasRoleIn()`

Verifica se um utilizador detém um papel delimitado a uma instância de contexto.

```php
Clearance::hasRoleIn($user, 'manager', $store);
```

Devolve `bool`.

## `resolveFor()`

Resolve todos os nomes de permissão efetivos que um utilizador detém dentro de um determinado contexto.

```php
Clearance::resolveFor($user, $store); // Collection<string>
```

Devolve uma `Collection<string>` de nomes de permissão. Ver [`ContextService::resolveFor()`](/pt/api/services.md#contextservice) para as regras de precedência das 3 fontes usadas para construir esta lista.

## `guards()`

Lista os nomes de todos os guards de autenticação geridos pelo Clearance.

```php
Clearance::guards(); // Collection<string>
```

Devolve uma `Collection<string>`. Apoiado por [`GuardService`](/pt/api/services.md#guardservice). Ver a [página de funcionalidades Guards](/pt/features/guards.md) para saber como os guards são configurados e injetados em `auth.guards`.

## Ver também

- [Traits & Diretivas](/pt/api/traits-directives.md) — os métodos de instância equivalentes via `HasClearance`, e as diretivas Blade seguras para convidados.
- [Serviços](/pt/api/services.md) — o `ContextService` subjacente e os serviços relacionados.
