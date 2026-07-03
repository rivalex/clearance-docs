# Super Admin

O papel `super_admin` é criado automaticamente pelo `clearance:install` e mantido sincronizado com todas as permissões `clearance-*`.

```bash
# Install and make user ID 1 a super admin
php artisan clearance:install --user=1
```

Ver o [guia de Instalação](/pt/guide/installation) e a [referência de comandos Artisan](/pt/api/artisan-commands) para a lista completa de flags do instalador.

## Bypass da Gate (opcional)

`super_admin_gate_bypass` controla se o papel `super_admin` contorna todas as verificações `can()` / `Gate::allows()` / política do Laravel globalmente, através de um hook `Gate::before()`. Padrão: `false`. O bypass é opcional. Para o ativar:

```php
// config/clearance.php
'super_admin_gate_bypass' => true,
```

::: danger Aplicado a toda a aplicação, não delimitado ao painel
Quando ativado, qualquer utilizador que detenha `super_admin` salta **todas** as verificações de Gate em toda a aplicação - não apenas as verificações do painel Clearance. Isto afeta todas as verificações `can()`, `Gate::allows()`, e política na sua aplicação, não apenas as que o Clearance define. Para um escopo mais restrito, deixe-o desativado e conceda permissões explicitamente em vez disso.
:::

## Deteção de aliases

Se a sua base de dados já contiver um papel chamado `super_admin`, `superadmin`, `super-admin`, `root`, ou `owner`, o instalador deteta-o e pergunta-lhe interativamente se pretende promovê-lo ou criar um `super_admin` separado. Use `--super-admin-role=NAME` para saltar o prompt:

```bash
php artisan clearance:install --super-admin-role=owner
```

::: tip A promoção não é destrutiva
A promoção renomeia o papel no próprio lugar - todas as atribuições `model_has_roles` existentes são preservadas via a FK em `role_id`. Nenhum utilizador perde a sua atribuição de papel existente durante a promoção.
:::

## Acesso ao Painel e Permissões de Escrita Granulares

Navegue até `/clearance` após a instalação. O acesso de leitura a todo o painel requer a permissão `clearance-access`, verificada via `can()`, nunca `hasRole()`:

```php
$user->givePermissionTo('clearance-access');
```

As operações de escrita em cada secção são controladas por uma permissão dedicada e delegável - todas criadas pelo `clearance:install` e atribuídas a `super_admin`:

| Permissão | Controla |
|---|---|
| `clearance-permissions-write` | Criar / editar / eliminar permissões |
| `clearance-roles-write` | Criar / editar / eliminar papéis e as suas atribuições de permissão |
| `clearance-guards-write` | Criar / editar / eliminar guards |
| `clearance-settings-write` | Editar metadados de exibição, ícones, cores, papel padrão |
| `clearance-users-write` | Atribuir / remover papéis para um utilizador específico |

```php
// Grant a user write access to roles only:
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-roles-write');
```

::: tip Conceder acesso restrito
Combine `clearance-access` com exatamente uma permissão `clearance-{section}-write` para dar a um utilizador (por exemplo, um líder de equipa) acesso de escrita a uma única secção do painel - papéis, por exemplo - sem expor as configurações, os guards, ou a gestão de utilizadores.
:::

Se uma linha de permissão granular não existir de todo na base de dados (um estado legado anterior à semeadura), a verificação recorre a `clearance-access` para compatibilidade retroativa. Em qualquer instância que já tenha executado `clearance:install`, este fallback nunca é ativado.

## Permissões `clearance-*` reservadas

As permissões `clearance-*` são reservadas: não podem ser criadas, renomeadas, eliminadas, ou adicionadas a qualquer papel, exceto através do próprio fluxo de instalação do pacote - mesmo por um ator `super_admin` para o próprio papel `super_admin`.

## Próximos passos

- [Permissões](/pt/features/permissions) - o painel CRUD de permissões geral (exclui `clearance-*`).
- [Papéis](/pt/features/roles) - atribuir permissões `clearance-*-write` a um papel personalizado.
- [Segurança](/pt/guide/security) - o modelo completo de escalonamento de privilégios e teto.
