# Comandos Artisan

O Clearance disponibiliza dois comandos Artisan: `clearance:install` para a configuração inicial, e `clearance:backfill` para semear padrões em instalações existentes. Ver o [guia de Instalação](/pt/guide/installation.md) para o percurso completo de configuração.

## `clearance:install`

```bash
php artisan clearance:install
php artisan clearance:install --user=1
php artisan clearance:install --role=editor
php artisan clearance:install --super-admin-role=owner
php artisan clearance:install --force
```

| Flag | Descrição |
|---|---|
| `--user=ID` | Atribui o papel `super_admin` a um utilizador após a instalação. |
| `--role=NAME` | Atribui `clearance-access` a um papel (criado se não existir). |
| `--super-admin-role=NAME` | Promove um papel existente a `super_admin` (salta o prompt interativo). |
| `--force` | Executa novamente mesmo que já esteja instalado. |

### O que faz, por ordem

1. Publica `config/clearance.php`.
2. Publica as migrações do Clearance.
3. Deteta as tabelas de Permissões do Spatie — se a tabela `roles` não existir, publica e executa primeiro as migrações do Spatie automaticamente.
4. Executa todas as migrações pendentes.
5. Cria `clearance-access` e todas as 5 permissões de capacidade granulares: `clearance-permissions-write`, `clearance-roles-write`, `clearance-guards-write`, `clearance-settings-write`, `clearance-users-write`.
6. Cria o papel `super_admin` com todas as permissões `clearance-*` atribuídas. Este passo é aditivo — nunca remove permissões existentes ao executar novamente.
7. Escreve `storage/.clearance-installed` como marcador de idempotência.

### Deteção de aliases

Se a base de dados já contiver um papel chamado `super_admin`, `superadmin`, `super-admin`, `root`, ou `owner`, o instalador deteta-o e pergunta interativamente se pretende promovê-lo ou criar um papel `super_admin` separado. Use `--super-admin-role=NAME` para saltar o prompt de forma não interativa.

A promoção renomeia o papel no próprio lugar — todas as atribuições `model_has_roles` são preservadas via a chave estrangeira em `role_id`.

::: warning
As permissões `clearance-*` são protegidas fora do fluxo de instalação — ver [`PermissionService`](/pt/api/services.md#permissionservice) e a `ClearanceProtectedResourceException` em [Serviços](/pt/api/services.md#exceções).
:::

## `clearance:backfill`

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

| Flag | Descrição |
|---|---|
| `--only=meta\|roles\|guards` | Executa apenas uma secção de backfill. |
| `--dry-run` | Pré-visualiza as alterações sem escrever. |

### Secções

| Secção | Descrição |
|---|---|
| `meta` | Semeia um nome de exibição `clr_meta` (nome do papel em title-case) para todos os papéis que não tenham um. |
| `roles` | Semeia os padrões de `clr_role_meta` (`scope=global`, `is_locked=false`) para todos os papéis que não tenham um. |
| `guards` | Importa guards de `config('auth.guards')` para `clr_guards`, filtrados por `allowed_guard_drivers`. |

Todas as secções são idempotentes — executar `clearance:backfill` repetidamente é seguro e apenas preenche o que está em falta.

::: tip
Para instalações Spatie pré-existentes: o `clearance:install` nunca executa as migrações do Spatie se a tabela `roles` já existir, pelo que os papéis, permissões, e atribuições existentes são preservados automaticamente.
:::

## Ver também

- [Serviços](/pt/api/services.md) — `GuardService` e `MetaService`, que suportam as secções de backfill `guards` e `meta`.
- [Guia de Migração](/pt/guide/migration.md) — migrar uma instalação Spatie Permission existente para o Clearance.
