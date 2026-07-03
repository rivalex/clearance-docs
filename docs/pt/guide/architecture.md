# Arquitetura

## Como funciona a resolução

O `resolveFor()` é o núcleo da autorização contextual do Clearance - ver [Autorização Contextual](/pt/features/contextual-authorization) para a API completa. Combina três fontes, por esta ordem de precedência:

1. **Concessões de papel contextuais** - permissões de papéis que o utilizador detém especificamente dentro deste contexto (`clr_role_ctx`).
2. **Concessões de papel globais** - permissões dos papéis do Spatie atribuídos globalmente ao utilizador (para que `super_admin` e outros papéis globais satisfaçam `canIn()` para qualquer contexto).
3. **Substituições por contexto** (`clr_ctx_overrides`) - `forced_on` acrescenta uma permissão às anteriores; `forced_off` remove-a, mesmo que a permissão tenha sido concedida por um papel global. Uma negação ganha sempre.

::: warning Uma negação ganha sempre
`forced_off` sobrepõe-se a qualquer permissão concedida por um papel global ou por uma concessão de papel contextual. Não há forma de "superar em prioridade" uma substituição `forced_off` para o mesmo utilizador/permissão/contexto, a não ser removendo-a.
:::

## Base de dados

O Clearance possui 6 tabelas e nunca altera as tabelas núcleo do Spatie (`roles`, `permissions`, `model_has_roles`, `model_has_permissions`, `role_has_permissions`). Todas as FKs que referenciam `roles.id`/`permissions.id` fazem cascade ao eliminar, salvo indicação contrária; as tabelas do Spatie não têm FKs a apontar de volta para `clr_*`.

| Tabela | Colunas-chave |
|---|---|
| `clr_meta` | `subject_type`, `subject_key` (únicos em conjunto) - `display_name`, `description`, `icon_svg`, `color` - metadados de exibição para um papel ou guard |
| `clr_role_meta` | `role_id` (único, FK -> `roles`) - `is_locked`, `scope` (`global`\|`contextual`), `context_types` (json) - `parent_role_id` (FK anulável -> `roles`, `ON DELETE SET NULL`) - papel-teto |
| `clr_guards` | `name` (único), `driver`, `provider` - guards geridos através do painel |
| `clr_settings` | `key` (único), `value` - armazenamento de chave/valor em tempo de execução (papel padrão, etc.) |
| `clr_role_ctx` | `user_id`, `role_id` (FK -> `roles`), `context_type`, `context_id` - atribuições de papel contextuais, únicas por `(user, role, context_type, context_id)` |
| `clr_ctx_overrides` | `user_id`, `role_id` (FK -> `roles`), `permission_id` (FK -> `permissions`), `context_type`, `context_id`, `type` (`forced_on`\|`forced_off`) - substituições de permissão por utilizador, por contexto |

## Nunca toca nas tabelas do Spatie

O Clearance foi concebido para coexistir com o `spatie/laravel-permission`, não para o substituir. Todo o registo `Role` e `Permission` que um painel Clearance cria ou edita é um modelo Spatie genuíno, guardado nas próprias tabelas do Spatie. O Clearance nunca executa uma migração que acrescente uma coluna a, ou escreva uma FK a partir de, qualquer uma das tabelas `roles`, `permissions`, `model_has_roles`, `model_has_permissions`, ou `role_has_permissions`.

Todos os dados próprios do Clearance - metadados de exibição, escopo e tetos de papel, atribuições contextuais, substituições por contexto, definições de guard, e configurações - residem exclusivamente nas 6 tabelas `clr_*` acima. Isto significa que:

- Desinstalar o Clearance nunca arrisca corromper os seus dados existentes de papéis/permissões do Spatie.
- Qualquer código que já leia diretamente os modelos ou tabelas do Spatie continua a funcionar sem modificações.
- As funcionalidades contextuais e de teto do Clearance são camadas puramente aditivas, resolvidas em tempo de consulta (via `resolveFor()` e `RoleService`), e não incorporadas no esquema do Spatie.

## Relacionado

- [Autorização Contextual](/pt/features/contextual-authorization) - `canIn()`, `hasRoleIn()`, diretivas Blade, `ContextService`
- [Papéis](/pt/features/roles) - escopo e papéis-teto, `RoleService::setParent()`
- [Migração](/pt/guide/migration) - fazer o backfill de `clr_meta`/`clr_role_meta`/`clr_guards` numa instalação Spatie existente
- [Segurança](/pt/guide/security) - como o modelo de teto e substituição previne o escalonamento de privilégios
