# Componentes Blade

O Clearance regista um pequeno conjunto de componentes Blade sob o namespace `clearance::`. Todos são reutilizáveis diretamente nas views da sua própria aplicação, não apenas dentro do painel.

## Componentes

### `x-clearance::message`

Exibição de mensagem de flash/estado.

```blade
<x-clearance::message>
    Changes saved successfully.
</x-clearance::message>
```

### `x-clearance::clipboard`

Botão/wrapper de copiar para a área de transferência — usado, por exemplo, para mostrar nomes de rota ou IDs com uma ação de cópia com um clique.

```blade
<x-clearance::clipboard :value="$route->name" />
```

### `x-clearance::validation-errors`

Renderizador padrão de lista de erros de validação do Laravel.

```blade
<x-clearance::validation-errors />
```

### `x-clearance::icon.*`

Um pequeno conjunto de ícones incluído, usado em todo o painel. Sem dependência externa de JS além do Alpine, que o Livewire já requer.

```blade
<x-clearance::icon.check />
```

## Componentes Livewire

Para referência, estes são os nomes completos dos componentes Livewire de página completa/incorporáveis registados pelo pacote, utilizáveis com a incorporação `<livewire:clearance::...>` nas suas próprias views:

| Nome do componente | Descrição |
|---|---|
| `dashboard` | O painel de controle. |
| `permissions.permission-manager` | Painel CRUD de Permissões. |
| `roles.role-manager` | Painel CRUD de Papéis, incluindo papéis-teto. |
| `guards.guard-manager` | Painel CRUD de Guards. |
| `settings.settings-manager` | Painel de Configurações (metadados de exibição, ícones, cores). |
| `users.user-clearance-manager` | Painel de atribuição de papéis/permissões por utilizador. Aceita `:userId`. |

```blade
<livewire:clearance::dashboard />

<livewire:clearance::users.user-clearance-manager :userId="$user->id" />
```

Ver as páginas de funcionalidades correspondentes a cada painel: [Painel de Controle](/pt/features/dashboard.md), [Permissões](/pt/features/permissions.md), [Papéis](/pt/features/roles.md), [Guards](/pt/features/guards.md), [Configurações](/pt/features/settings.md), [Utilizadores](/pt/features/users.md).

::: tip
A chave `config('clearance.ui.flux_pro')` controla se os elementos Flux UI exclusivos de Pro são renderizados nestes componentes. `null` (o padrão) deteta automaticamente o Flux Pro via `Flux::pro()`.
:::
