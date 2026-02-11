# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

NovaTech Solutions is a tech consultancy website built with vanilla HTML/CSS/JS (no frameworks, no build step). Serves as a learning environment for the Advanced Claude Code Pluralsight course.

## Commands

```bash
npm run dev            # Start local server (serves src/ on localhost:3000)
npm run test           # Run all tests (unit then e2e, sequential)
npm run test:unit      # Unit tests only (Node.js native test runner)
npm run test:e2e       # Playwright E2E tests (auto-starts dev server)
npm run test:e2e:ui    # Playwright E2E with interactive UI
npm run lint           # ESLint on src/js/
npm run lint:fix       # ESLint with auto-fix
npm run format         # Prettier format src/**/*.{html,css,js}
npm run format:check   # Prettier check without writing
```

Single unit test file: `node --test tests/unit/validation.test.js`
Single e2e test: `npx playwright test tests/e2e/navigation.spec.js`

Dev server URL: `http://localhost:3000/pages/index.html`

## Architecture

**No build step.** Files in `src/` are served directly. `"type": "module"` in package.json makes all `.js` files ES modules.

### JavaScript Module Pattern

Each JS module auto-initializes on DOMContentLoaded (no central entry point). Pages load only the modules they need via `<script type="module">`. Every module guards against missing DOM elements with early returns.

- `validation.js` — Pure validation utilities (no DOM deps), testable in isolation. Returns `{ isValid, message }` objects.
- `contact-form.js` — Imports validation.js, handles form submission with simulated async API call (1.5s delay).
- `navigation.js` — Mobile menu toggle with keyboard support (escape key, click outside).
- `portfolio-filters.js` — Category filtering via `data-filter`/`data-category` attributes using event delegation.

### CSS Load Order (critical)

Each HTML page loads stylesheets in this order:
1. `variables.css` — Design tokens (colors, typography, spacing, shadows, transitions)
2. `base.css` — Reset, accessibility utilities (`.visually-hidden`, `.skip-link`), container system
3. `components.css` — Shared components (header, footer, buttons, forms)
4. `pages/{pagename}.css` — Page-specific styles

### HTML Structure

All 5 pages share identical header/nav and footer (manually duplicated, no templating). Changes to shared elements require updating all pages. Each page follows: skip-link → header → main#main-content → footer → scripts.

### Testing

- **Unit tests** (`tests/unit/`): Node.js native `node:test` + `node:assert`. Tests validation logic in isolation.
- **E2E tests** (`tests/e2e/`): Playwright. `playwright.config.js` auto-starts dev server. Mobile viewport tests use `{ width: 375, height: 667 }`.

## Code Conventions

### HTML
- Semantic elements with ARIA labels (`aria-labelledby`, `aria-current="page"`, `aria-expanded`)
- Keep pages under 200 lines

### CSS
- BEM naming: `.block__element--modifier`; state classes use `.is-*` pattern (e.g., `.is-open`, `.is-invalid`)
- All values should use CSS custom properties from `variables.css`
- Mobile-first; responsive breakpoint at 768px

### JavaScript
- ES6 modules with named exports
- JSDoc comments for public functions
- No global variables

## Git Workflow

Branches: `main` (production), `feature/*`, `bugfix/*`

Commit format: `type: description` (e.g., `feat:`, `fix:`, `docs:`, `chore:`)

## Current Focus Areas

- Accessibility improvements (WCAG 2.1 AA)
- Form validation enhancements
- Mobile navigation refinements
