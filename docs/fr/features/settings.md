# Paramètres

Le panneau Paramètres configure l'affichage des rôles et des gardes dans le reste du panneau Clearance, ainsi que le rôle par défaut attribué aux nouveaux utilisateurs.

## Ce que fait le panneau

- **Métadonnées d'affichage** par rôle/garde - nom d'affichage, description, et une icône SVG assainie + couleur hexadécimale, affichées dans tout le panneau.
- **Rôle par défaut** - le rôle automatiquement attribué aux utilisateurs nouvellement inscrits lorsque `auto_assign_default_role` vaut `true`.
- **Attribution en masse du rôle par défaut** - attribution en un clic du rôle par défaut actuel à tous les utilisateurs existants qui ne l'ont pas encore.
- **Afficher les icônes** - active/désactive l'affichage des icônes dans tout le panneau.

<Screenshot src="/screenshots/settings-page.png" alt="Panneau Paramètres de Clearance avec le rôle par défaut et le bouton d'affichage des icônes" caption="Le panneau Paramètres contrôle le rôle par défaut, l'attribution en masse, et l'affichage des icônes." />

## Métadonnées d'affichage par rôle/garde

<Screenshot src="/screenshots/settings-role-meta.png" alt="Formulaire de métadonnées d'affichage de rôle avec sélecteurs d'icône et de couleur" caption="Modification du nom d'affichage, de la description, de l'icône, et de la couleur d'un rôle." />

Chaque icône SVG fournie par l'utilisateur passe par un assainisseur en liste blanche avant le stockage puis à nouveau au moment du rendu, afin que les métadonnées d'affichage puissent être modifiées sans introduire de balisage non sûr dans le panneau.

## Rôle par défaut et attribution automatique

```php
// config/clearance.php
'auto_assign_default_role' => true, // requires a default role set in Settings
```

::: tip Définissez le rôle par défaut avant d'activer l'attribution automatique
`auto_assign_default_role` n'a aucun effet tant qu'un rôle par défaut n'est pas configuré dans le panneau Paramètres. Définissez d'abord le rôle par défaut, puis activez le drapeau.
:::

Utilisez **l'attribution en masse** dans le panneau à tout moment pour attribuer rétroactivement le rôle par défaut actuel aux utilisateurs existants qui ne l'ont pas encore - utile après un changement de rôle par défaut, ou après l'activation de `auto_assign_default_role` sur une application ayant déjà des utilisateurs.

## Contrôle d'accès

L'accès en lecture nécessite `clearance-access`. Modifier les métadonnées d'affichage, les icônes, les couleurs, ou le rôle par défaut nécessite `clearance-settings-write` :

```php
$user->givePermissionTo('clearance-access');
$user->givePermissionTo('clearance-settings-write');
```

## Route et composant intégré

| URI | Nom de route | Description |
|---|---|---|
| `/clearance/settings` | `clearance.settings` | Métadonnées d'affichage (icônes, couleurs), rôle par défaut, paramètres généraux |

```blade
<livewire:clearance::settings.settings-manager />
```

## Base de données

| Table | Colonnes clés |
|---|---|
| `clr_meta` | `subject_type`, `subject_key` (uniques ensemble) - `display_name`, `description`, `icon_svg`, `color` |
| `clr_settings` | `key` (unique), `value` |

## Prochaines étapes

- [Rôles](/fr/features/roles) - les rôles dont les métadonnées d'affichage sont configurées ici.
- [Gardes](/fr/features/guards) - les gardes dont les métadonnées d'affichage sont configurées ici.
- [Sécurité](/fr/guide/security) - comment fonctionne l'assainisseur SVG.
