# Painel de Controle

O Painel de Controle é a página inicial do painel Clearance. Dá um resumo instantâneo do estado de autorização da sua aplicação - sem necessidade de configuração.

## O que mostra

- **Total de papéis** - contagem de todos os papéis do Spatie.
- **Total de guards** - contagem de todos os guards geridos pelo Clearance (ver [Guards](/pt/features/guards)).
- **Total de permissões** - contagem de todas as permissões do Spatie.
- **Grupos de permissões** - o número de grupos de permissões distintos, derivado do prefixo de nomenclatura (o `group` em `group-action`, ex. `orders` em `orders-create`). Ver [Permissões](/pt/features/permissions) para a convenção de nomenclatura.
- **Atribuições de papel contextuais** - o número de atribuições de papel delimitadas a uma instância de modelo específica (uma Store, um Tenant, um Project). Ver [Autorização Contextual](/pt/features/contextual-authorization).
- **Top 5 papéis por número de utilizadores** - os cinco papéis com mais utilizadores atribuídos, ordenados.

<Screenshot src="/screenshots/dashboard-page.png" alt="Painel de Controle do Clearance a mostrar contagens de papéis, guards e permissões" caption="O Painel de Controle resume papéis, guards, permissões, grupos, atribuições contextuais, e os principais papéis." />

## Aceder ao Painel de Controle

| URI | Nome da rota | Descrição |
|---|---|---|
| `/clearance` | `clearance.home` | Painel de Controle |

Ler o Painel de Controle requer a permissão `clearance-access`, tal como qualquer outra secção do painel. Ver [Super Admin](/pt/features/super-admin#acesso-ao-painel-e-permissões-de-escrita-granulares) para a tabela completa de controlo de acesso.

## Incorporar o componente

O Painel de Controle é um componente Livewire autónomo. Monte-o em qualquer ponto dos seus próprios layouts em vez de - ou além de - a rota pré-construída `/clearance`:

```blade
<livewire:clearance::dashboard />
```

## Próximos passos

- [Permissões](/pt/features/permissions) - gerir a lista de permissões que alimenta as contagens do Painel de Controle.
- [Papéis](/pt/features/roles) - gerir os papéis por trás da estatística "top 5 por número de utilizadores".
- [Autorização Contextual](/pt/features/contextual-authorization) - o que é uma atribuição de papel contextual.
