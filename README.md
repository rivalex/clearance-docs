# clearance-docs

Documentation site for the [Clearance](https://github.com/rivalex/clearance) Laravel package.

Built with [VitePress](https://vitepress.dev) and deployed to GitHub Pages at [rivalex.github.io/clearance-docs](https://rivalex.github.io/clearance-docs/).

## Local development

```bash
npm install
npm run docs:dev
```

Open http://localhost:5173/clearance-docs/

## Build

```bash
npm run docs:build
npm run docs:preview
```

## Adding screenshots

Place screenshots in `docs/public/screenshots/`. Reference them in markdown with the
`<Screenshot>` component:

```markdown
<Screenshot src="/screenshots/my-screenshot.png" alt="Description" caption="Caption text" />
```

Recommended screenshot filenames:
- `dashboard-page.png` — dashboard overview
- `permissions-page.png` — permission manager
- `permission-form-modal.png` — new/edit permission modal
- `roles-page.png` — role manager
- `role-form-ceiling.png` — role form with ceiling/parent role selection
- `guards-page.png` — guard manager
- `settings-page.png` — settings panel (display metadata)
- `settings-role-meta.png` — role meta table (icon/color editor)
- `user-panel-global.png` — user panel, global role assignment
- `user-panel-contextual.png` — user panel, contextual role + overrides

## Deployment

Push to `main` and the GitHub Actions workflow deploys automatically to GitHub Pages.

**First-time setup:**
1. Go to the repository Settings → Pages
2. Set Source to **GitHub Actions**
3. Push to `main`
