# Migrar a partir de uma instalação Spatie existente

Se a sua aplicação já usa `spatie/laravel-permission` com papéis, permissões e atribuições existentes, o `clearance:install` deteta-os e preserva-os - nunca executa as migrações do Spatie se a tabela `roles` já existir. Ver [Instalação](/pt/guide/installation) para o fluxo de instalação completo.

Após a instalação, use `clearance:backfill` para adotar os próprios metadados do Clearance nos seus papéis e guards existentes.

```bash
php artisan clearance:backfill              # meta + role scope + guards
php artisan clearance:backfill --only=meta  # just one section
php artisan clearance:backfill --dry-run    # preview, no writes
```

## O que cada secção faz

| Secção | Efeito |
|---|---|
| `meta` | Semeia um nome de exibição `clr_meta` (nome do papel em title-case) para todos os papéis que não tenham um. |
| `roles` | Semeia os padrões de `clr_role_meta` (`scope=global`, `is_locked=false`) para todos os papéis que não tenham um. |
| `guards` | Importa guards de `config('auth.guards')` para `clr_guards`, filtrados por `allowed_guard_drivers`. |

Use `--only=<section>` para executar uma única secção em vez das três.

::: tip Idempotente
Todas as secções são idempotentes - executar `clearance:backfill` novamente é seguro e não duplicará nem sobrescreverá linhas `clr_*` existentes.
:::

## Casos limite

O repositório do pacote tem um guia de migração mais aprofundado que cobre casos limite não resumidos aqui - nomeadamente o que acontece quando a sua base de dados já tem um papel chamado `super_admin` (ou um alias como `superadmin`, `root`, `owner`) que colide com o próprio papel `super_admin` do Clearance, e notas sobre subclasses de modelo `Role`/`Permission` personalizadas e o comportamento de importação de guards. Leia-o antes de fazer o backfill de uma base de dados de produção com dados de papéis existentes não triviais. Ver também [Super Admin](/pt/features/super-admin) para o comportamento de deteção de aliases durante o `clearance:install`.

## Relacionado

- [Instalação](/pt/guide/installation) - o fluxo completo do `clearance:install`
- [Comandos Artisan](/pt/api/artisan-commands) - referência completa de comandos
- [Arquitetura](/pt/guide/architecture) - as tabelas `clr_*` que o backfill preenche
