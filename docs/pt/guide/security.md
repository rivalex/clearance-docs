# Segurança

O Clearance passou por uma auditoria de segurança dedicada antes da publicação, mais duas passagens de reforço subsequentes antes do lançamento. Destaques: tetos contra escalonamento de privilégios em todos os caminhos de concessão de permissões (tanto concessões diretas como sincronização ao nível do papel), substituições de contexto com autoridade de negação, um sanitizador de SVG com lista de permissões, um namespace de permissões `clearance-*` protegido, e diretivas Blade seguras para convidados.

## Acesso ao painel e permissões de escrita granulares

O acesso de leitura a todo o painel requer a permissão `clearance-access` (verificada via `can()`, nunca `hasRole()`). As operações de escrita em cada secção são controladas por uma permissão dedicada e delegável - todas criadas pelo `clearance:install` e atribuídas a `super_admin`:

| Permissão | Controla |
|---|---|
| `clearance-permissions-write` | Criar / editar / eliminar permissões |
| `clearance-roles-write` | Criar / editar / eliminar papéis e as suas atribuições de permissão |
| `clearance-guards-write` | Criar / editar / eliminar guards |
| `clearance-settings-write` | Editar metadados de exibição, ícones, cores, papel padrão |
| `clearance-users-write` | Atribuir / remover papéis para um utilizador específico |

Se uma linha de permissão granular não existir de todo na base de dados (um estado legado anterior à semeadura), a verificação recorre a `clearance-access` para compatibilidade retroativa.

## Namespace `clearance-*` protegido

As permissões `clearance-*` são reservadas: não podem ser criadas, renomeadas, eliminadas, ou adicionadas a qualquer papel, exceto através do próprio fluxo de instalação do pacote - mesmo por um ator `super_admin` para o próprio papel `super_admin`.

## Tetos contra escalonamento de privilégios

Todos os caminhos de concessão de permissões no Clearance aplicam um teto, para que um ator nunca possa conceder mais do que ele próprio detém:

- **Papéis-teto** - um papel pode declarar um papel principal cujo conjunto de permissões atua como limite superior. Qualquer permissão em excesso é silenciosamente cortada em `RoleService::setParent()` e em toda a sincronização de permissões subsequente - sem exceção, sem mensagem de erro. Remover permissões de um papel principal propaga a remoção automaticamente a todos os papéis filho. Ver [Papéis](/pt/features/roles) para a API completa de tetos.
- **Módulo de Utilizadores** - tanto a atribuição de papéis como as concessões de permissão diretas/de substituição aplicam um teto de privilégio: um ator que não seja `super_admin` não pode modificar as suas próprias atribuições, nem pode conceder uma permissão que ele próprio não detenha. Ver [Utilizadores](/pt/features/users).

## Substituições de contexto com autoridade de negação

As substituições de permissão por contexto (`clr_ctx_overrides`) suportam `forced_on` e `forced_off`. `forced_off` ganha sempre - mesmo sobre uma permissão concedida por um papel global. Não existe nenhum caminho que permita a uma concessão de menor precedência sobrepor-se a uma negação explícita. Ver [Arquitetura](/pt/guide/architecture#como-funciona-a-resolução) para a ordem de resolução completa.

## Sanitização de SVG

Todo o ícone fornecido pelo utilizador (metadados de exibição de papel/guard no painel de Configurações) passa por um sanitizador com lista de permissões, tanto antes do armazenamento como no momento da renderização - não uma verificação única no upload.

## Diretivas Blade seguras para convidados

`@canin` e `@hasrolein` resolvem-se ambas de forma segura (devolvem `false`) para um utilizador convidado - nunca lançam uma exceção.

## O bypass da Gate é aplicado a toda a aplicação

`super_admin_gate_bypass` (padrão `false`) controla se o papel `super_admin` contorna todas as verificações `can()` / `Gate::allows()` / política do Laravel globalmente, através de um hook `Gate::before()`.

::: warning Não delimitado ao painel
Quando ativado, qualquer utilizador que detenha `super_admin` salta todas as verificações de Gate em toda a aplicação - não apenas as verificações do painel Clearance. Ative-o deliberadamente. Ver [Super Admin](/pt/features/super-admin) para o comportamento de deteção de aliases e promoção.
:::

## Pormenores relevantes para a segurança

- `forced_off` ganha sempre - mesmo sobre uma permissão concedida por um papel global.
- Um papel-teto apenas corta, nunca concede - definir um papel principal permissivo não concede nada ao filho por si só.
- O bypass da Gate (`super_admin_gate_bypass`) aplica-se a toda a aplicação, não apenas ao painel.
- O módulo de Utilizadores está desativado por padrão (`modules.users = false`).
- As permissões `clearance-*` são intocáveis fora do fluxo de instalação, mesmo para `super_admin`.
- Execute sempre `clearance:install`, nunca um `migrate` simples, numa instalação nova - ver [Instalação](/pt/guide/installation).

## Relacionado

- [Arquitetura](/pt/guide/architecture) - precedência de resolução e o esquema `clr_*`
- [Papéis](/pt/features/roles) - papéis-teto em profundidade
- [Utilizadores](/pt/features/users) - o teto de privilégio na atribuição de papéis/permissões
- [Super Admin](/pt/features/super-admin) - bypass da Gate e deteção de aliases
