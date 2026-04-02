# CLAUDE.md

This file provides guidance to AI assistants working on this codebase.

## Project Overview

This is a minimal Next.js 15 starter template optimized for deployment on Netlify. It is maintained by Netlify as a reference template and kept intentionally lightweight — a starting point, not a full application.

## Commands

```bash
npm run dev      # Start development server (http://localhost:3000)
npm run build    # Production build
npm run start    # Start production server locally
npm run export   # Build + export as static site
```

No test runner is configured. The README references Cypress for browser testing, but Cypress is not installed or configured in this repository.

## Architecture

```
next-netlify-starter/
├── components/         # Reusable UI components
│   ├── Header.js       # Accepts a `title` prop, renders <h1>
│   ├── Footer.js       # Netlify logo footer, no props
│   └── Footer.module.css
├── pages/
│   ├── _app.js         # Custom App wrapper, imports global styles
│   └── index.js        # Home page
├── public/             # Static assets served at /
│   ├── favicon.ico
│   └── logo-netlify.svg
├── styles/
│   └── globals.css     # Global CSS, imported in _app.js
├── next.config.js      # Minimal: only reactStrictMode: true
├── jsconfig.json       # Path aliases
├── netlify.toml        # Netlify build config
└── renovate.json       # Automated dependency updates config
```

## Key Conventions

### Imports — use path aliases, not relative paths
```js
// Good
import Header from '@components/Header'
import styles from '@styles/globals.css'

// Avoid
import Header from '../../components/Header'
```

Aliases are defined in `jsconfig.json`:
- `@components/*` → `components/*`
- `@styles/*` → `styles/*`

### Styling
- **Global styles**: `styles/globals.css`, imported once in `pages/_app.js`
- **Component styles**: CSS Modules (e.g., `Footer.module.css`), co-located with the component
- Do not mix these approaches in the same component

### Components
- Plain JavaScript (no TypeScript)
- Functional components only
- No state management library — use React built-ins if needed
- Props are not typed (no PropTypes); keep interfaces simple and documented via JSDoc if needed

### Pages
- Follow Next.js Pages Router conventions (`pages/` directory)
- No App Router (`app/`) — do not introduce it without discussion

## Dependencies

| Package | Purpose |
|---|---|
| `next` | Framework (v15) |
| `react` / `react-dom` | UI library (v18) |
| `@netlify/plugin-nextjs` | Netlify deployment adapter |

No UI component libraries, state managers, or testing frameworks are installed. Keep the dependency footprint minimal.

## Deployment

- **Platform**: Netlify
- **Build command**: `npm run build`
- **Publish directory**: `.next`
- **Node version**: 18 (specified in `.nvmrc`)

The `@netlify/plugin-nextjs` plugin enables SSR, API routes, and image optimization on Netlify automatically.

## Dependency Management

Renovate is configured via `renovate.json` and runs automated PRs to keep dependencies up to date. Do not manually pin versions unless there is a specific reason — let Renovate handle updates.

## Development Branch

Active development for this session happens on branch `claude/add-claude-documentation-iRekQ`. The primary branch is `main`.

## What to Avoid

- Do not add the App Router (`app/` directory) — this project uses the Pages Router
- Do not add TypeScript without explicit request
- Do not add a CSS framework (Tailwind, etc.) without explicit request
- Do not install testing libraries unless setting up an actual test suite
- Do not add environment variable files (`.env`) to version control
- Keep `next.config.js` minimal; only add options that are needed
