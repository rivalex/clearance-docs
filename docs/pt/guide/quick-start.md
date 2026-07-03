# Início Rápido

Após a [instalação](/pt/guide/installation), existem duas formas de expor o painel Clearance: as rotas pré-construídas, ou incorporar componentes Livewire individuais em qualquer ponto da sua aplicação.

## Opção A - Rotas pré-construídas

Após a instalação, o painel fica disponível em `/clearance` (ou o `route_prefix` configurado, ver [Configuração](/pt/guide/configuration)). Todas as rotas são protegidas pelo middleware `clearance.access` e usam automaticamente o layout Livewire da aplicação hospedeira.

| URI | Nome da rota | Descrição |
|---|---|---|
| `/clearance` | `clearance.home` | Painel de Controle |
| `/clearance/guards` | `clearance.guards` | Gestão de Guards (criar/editar/eliminar) |
| `/clearance/permissions` | `clearance.permissions` | CRUD de Permissões com validação de nomenclatura por prefixo de grupo |
| `/clearance/roles` | `clearance.roles` | CRUD de Papéis com atribuição de permissões delimitada ao guard, escopo (global/contextual), e teto |
| `/clearance/settings` | `clearance.settings` | Metadados de exibição (ícones, cores), papel padrão, configurações gerais |
| `/clearance/user/{userId}` | `clearance.user` | Atribuição de papéis por utilizador + substituições de permissão contextuais (requer `modules.users = true`) |

Os nomes de rota mantêm-se estáveis independentemente de alterações ao prefixo. Para personalizar o middleware:

```php
// config/clearance.php
'middleware' => ['web', 'auth', 'verified'],
```

## Opção B - Componentes Livewire incorporados

Cada secção do painel pode ser montada individualmente dentro de qualquer view Blade, página Filament, ou rota personalizada:

```blade
<livewire:clearance::dashboard />
<livewire:clearance::permissions.permission-manager />
<livewire:clearance::roles.role-manager />
<livewire:clearance::guards.guard-manager />
<livewire:clearance::settings.settings-manager />
{{-- Users module (requires modules.users = true in config) --}}
<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

## Para onde ir a seguir

- [Referência de configuração](/pt/guide/configuration) - todas as chaves de configuração, padrões e finalidade
- [Autorização Contextual](/pt/features/contextual-authorization) - delimitar papéis e permissões a uma Store, Tenant, ou Project
- [Papéis](/pt/features/roles) - escopo e papéis-teto
- [Painel de Controle](/pt/features/dashboard) - o que o componente do Painel de Controle mostra
- [Utilizadores](/pt/features/users) - o painel opcional por utilizador
