# Introdução

## O que é o Clearance?

O Clearance é um painel de administração pronto a usar para aplicações Laravel, construído sobre o [spatie/laravel-permission](https://github.com/spatie/laravel-permission). Acrescenta um painel Livewire 4 + Flux UI para gerir permissões, papéis, guards e configurações, mais uma camada de autorização contextual e tetos de papéis - instalável com um único comando artisan, sem alterar qualquer tabela do Spatie.

O Clearance oferece-lhe:

- **Painel de administração** para Permissões, Papéis, Guards, Configurações e um Painel de Controle - use as rotas pré-construídas ou incorpore cada componente Livewire individualmente.
- **Autorização contextual** - conceda um papel ou permissão delimitado a uma instância de modelo específica (uma Store, um Tenant, um Project), com `$user->canIn()`, diretivas Blade `@canin`/`@hasrolein`, e um `ContextService` para uso fora do modelo User.
- **Papéis-teto** - um papel pode declarar um papel principal cujo conjunto de permissões atua como limite superior; um papel filho nunca pode exceder o seu papel principal, aplicado automaticamente a cada gravação.
- **Permissões de escrita granulares** - uma permissão delegável `clearance-{section}-write` por secção do painel, para que possa atribuir "gerir papéis" sem também atribuir "gerir configurações."
- **Super Admin** com um bypass global e opcional da Gate, deteção de aliases para papéis do tipo administrador já existentes, e promoção segura e não destrutiva.
- **Módulo de Utilizadores** (opcional) - atribua papéis globalmente ou a um contexto específico, mais substituições de permissão por contexto, a partir de um painel por utilizador.
- **Painel de Configurações** - nome de exibição, descrição, ícone SVG sanitizado e cor por papel/guard; um papel padrão configurável com atribuição automática e atribuição em massa.
- **Gestão de Guards** - guards apoiados em base de dados, injetados em `auth.guards` no arranque, restritos a um conjunto de drivers em lista de permissões.
- **9 idiomas incluídos** (ar, en, es, fr, hi, it, pt, ru, zh), testados quanto à paridade de chaves.
- **Pontos de extensibilidade** - trait `HasClearance`, trait `HasPermissionGroups`, contrato `ClearanceContextable`, componentes Blade reutilizáveis.
- **Sanitização de SVG** - todo o ícone fornecido pelo utilizador passa por um sanitizador com lista de permissões antes do armazenamento e no momento da renderização.
- **Reforçado em segurança** - tetos contra escalonamento de privilégios em todos os caminhos de concessão de permissões, substituições de contexto com autoridade de negação, namespace `clearance-*` protegido. Ver [Segurança](/pt/guide/security).

## Relação com o spatie/laravel-permission

O Clearance não substitui o pacote de permissões do Spatie - é uma camada de UI e autorização sobre ele. Os papéis e permissões continuam a ser os modelos `Role` e `Permission` do Spatie, continuando a ser guardados nas tabelas `roles`, `permissions`, `model_has_roles`, `model_has_permissions` e `role_has_permissions` do Spatie. O Clearance nunca altera essas tabelas. Tudo o que o Clearance acrescenta - metadados de exibição, escopo de papel, tetos, atribuições contextuais, substituições por contexto - reside no seu próprio conjunto de tabelas `clr_*`. Ver [Arquitetura](/pt/guide/architecture) para a descrição completa.

## Requisitos

| Dependência | Versão |
|---|---|
| PHP | ^8.3 |
| Laravel | ^11.0 \| ^12.0 \| ^13.0 |
| spatie/laravel-permission | ^6.0 |
| livewire/livewire | ^3.0 \| ^4.0 |
| livewire/flux | ^2.14 |

## Conceitos-chave

| Conceito | Resumo |
|---|---|
| Permissão | Uma permissão do Spatie que segue a convenção de nomenclatura `group-action` (ex. `orders-create`). Concede uma habilidade. |
| Escopo do papel | Todo o papel é `global` (atribuído diretamente, aplica-se em todo o lado) ou `contextual` (vinculado a tipos de modelo específicos, apenas atribuível dentro de uma instância de contexto). |
| Papel-teto | Um papel pode declarar um papel principal cujo conjunto de permissões é um limite superior - o filho nunca o pode exceder. |
| Contexto | Uma instância de modelo (uma Store, um Tenant, um Project) à qual um papel ou uma substituição de permissão pode ser delimitado. |
| Substituição | Uma concessão ou negação de permissão `forced_on`/`forced_off` por utilizador, por contexto - uma negação ganha sempre. |

Cada um destes conceitos tem uma página completa: [Permissões](/pt/features/permissions), [Papéis](/pt/features/roles), [Autorização Contextual](/pt/features/contextual-authorization), [Utilizadores](/pt/features/users), [Super Admin](/pt/features/super-admin).

## Próximos passos

Pronto para instalar o Clearance? Continue para o [guia de Instalação](/pt/guide/installation).
