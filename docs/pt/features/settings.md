# Configurações

O painel de Configurações define como os papéis e guards são exibidos em todo o restante painel Clearance, além do papel padrão atribuído a novos utilizadores.

## O que o painel faz

- **Metadados de exibição** por papel/guard - nome de exibição, descrição, e um ícone SVG sanitizado + cor hexadecimal, exibidos em todo o painel.
- **Papel padrão** - o papel atribuído automaticamente a utilizadores recém-registados quando `auto_assign_default_role` é `true`.
- **Atribuição em massa do papel padrão** - atribuição com um clique do papel padrão atual a todos os utilizadores existentes que ainda não o tenham.
- **Mostrar ícones** - alterna a exibição de ícones em todo o painel.

<Screenshot src="/screenshots/settings-page.png" alt="Painel de Configurações do Clearance com papel padrão e alternador de mostrar ícones" caption="O painel de Configurações controla o papel padrão, a atribuição em massa, e a exibição de ícones." />

## Metadados de exibição por papel/guard

<Screenshot src="/screenshots/settings-role-meta.png" alt="Formulário de metadados de exibição de papel com seletores de ícone e cor" caption="Editar o nome de exibição, descrição, ícone, e cor de um papel." />

Todo o ícone SVG fornecido pelo utilizador passa por um sanitizador com lista de permissões antes do armazenamento e novamente no momento da renderização, para que os metadados de exibição possam ser editados sem introduzir markup inseguro no painel.

## Papel padrão e atribuição automática

```php
// config/clearance.php
'auto_assign_default_role' => true, // requires a default role set in Settings
```

::: tip Defina o papel padrão antes de ativar a atribuição automática
`auto_assign_default_role` não tem qualquer efeito até que um papel padrão seja configurado no painel de Configurações. Defina primeiro o papel padrão, depois ative a opção.
:::

Use a **Atribuição em massa** no painel a qualquer momento para atribuir retroativamente o papel padrão atual aos utilizadores existentes que ainda não o tenham - útil após alterar o papel padrão, ou após ativar `auto_assign_default_role` numa aplicação com utilizadores já existentes.

## Controlo de acesso

O acesso de leitura requer `clearance-access`. Editar metadados de exibição, ícones, cores, ou o papel padrão requer `clearance-settings-write`:

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-settings-write');
```

## Rota e componente incorporado

| URI | Nome da rota | Descrição |
|---|---|---|
| `/clearance/settings` | `clearance.settings` | Metadados de exibição (ícones, cores), papel padrão, configurações gerais |

```blade
<livewire:clearance::settings.settings-manager />
```

## Base de dados

| Tabela | Colunas-chave |
|---|---|
| `clr_meta` | `subject_type`, `subject_key` (únicos em conjunto) - `display_name`, `description`, `icon_svg`, `color` |
| `clr_settings` | `key` (único), `value` |

## Próximos passos

- [Papéis](/pt/features/roles) - os papéis cujos metadados de exibição são configurados aqui.
- [Guards](/pt/features/guards) - os guards cujos metadados de exibição são configurados aqui.
- [Segurança](/pt/guide/security) - como funciona o sanitizador de SVG.
