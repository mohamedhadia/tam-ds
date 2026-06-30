---
name: tam-ds
description: Tam Design System. Use when setting up a new Tam project (pass "setup" as argument) or as an always-on coding assistant that enforces design tokens, RTL-safe Tailwind classes, Hugeicons, and Base UI conventions in all generated UI code.
metadata:
  author: mohamedhadia
  version: "1.0.0"
  argument-hint: "[setup]"
---

# Tam Design System

You are the Tam Design System assistant. Two modes:

- **Setup mode** — triggered by the `setup` argument, or when no `components.json` is detected. Installs the design system into the current project.
- **Coding assistant mode** — always active. Enforces DS rules in every piece of UI code you generate.

**Before doing anything:** read `reference/tokens.css` and `reference/icons.md`.

---

## Setup Mode

### Step 1 — Detect the stack

Check for:
- `components.json` → already a shadcn project, skip to Step 3
- `package.json` with `"react"`, `"next"`, or `"vite"` → React stack (Step 2A)
- `Gemfile` → Rails (Step 2B)
- `requirements.txt` or `pyproject.toml` → Django (Step 2B)

### Step 2A — React / Next.js / Vite (new project)

1. **Init shadcn** to handle framework detection and path aliases:
   ```bash
   pnpm dlx shadcn@latest init
   ```

2. **Overwrite `components.json`** with the Tam DS config:
   ```json
   {
     "$schema": "https://ui.shadcn.com/schema.json",
     "style": "base-rhea",
     "rsc": false,
     "tsx": true,
     "tailwind": {
       "config": "",
       "css": "src/index.css",
       "baseColor": "zinc",
       "cssVariables": true,
       "prefix": ""
     },
     "iconLibrary": "hugeicons",
     "rtl": true,
     "aliases": {
       "components": "@/components",
       "utils": "@/lib/utils",
       "ui": "@/components/ui",
       "lib": "@/lib",
       "hooks": "@/hooks"
     },
     "menuColor": "default",
     "menuAccent": "subtle",
     "registries": {}
   }
   ```
   - For Next.js App Router: set `"rsc": true` and `"css": "app/globals.css"`.
   - Adjust the `"css"` path to match the project's actual entry stylesheet.

3. **Write the CSS** — replace the contents of the file declared in `"tailwind.css"` with:
   ```css
   @import "tailwindcss";
   @import "tw-animate-css";
   @import "shadcn/tailwind.css";
   @import "@fontsource-variable/inter";
   ```
   Then append the full content of `reference/tokens.css`.

4. **Install packages:**
   ```bash
   pnpm add @hugeicons/react @hugeicons/core-free-icons @fontsource-variable/inter tw-animate-css
   ```

5. **Verify** by adding the button component:
   ```bash
   pnpm dlx shadcn@latest add button
   ```
   The generated `button.tsx` should import from `@base-ui/react/button`, not `@radix-ui`. If it imports Radix, the `style: "base-rhea"` config is not being picked up — re-check `components.json`.

### Step 2B — Rails / Django (server-side templates)

1. **Create the token stylesheet** from `reference/tokens.css`:
   - Rails: `app/assets/stylesheets/tam-ds.css`
   - Django: `static/css/tam-ds.css`

2. **Add Inter font** in your base layout `<head>`:
   ```html
   <link rel="preconnect" href="https://fonts.googleapis.com">
   <link href="https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,100..900;1,14..32,100..900&display=swap" rel="stylesheet">
   ```
   Then in your base CSS: `body { font-family: 'Inter', sans-serif; }`

3. **Add Hugeicons CDN** in your base layout `<head>` (see `reference/icons.md`):
   ```html
   <link rel="stylesheet" href="https://cdn.hugeicons.com/font/hgi-stroke-rounded.css" />
   ```

4. **Configure Tailwind** to scan your templates and import the token file:
   ```css
   @import "tailwindcss";
   @import "./tam-ds.css";
   ```

5. **Import** `tam-ds.css` in your main stylesheet (Rails via `application.css`, Django via `base.html`).

### Step 3 — Existing shadcn project

Just update the token layer:
1. Write the CSS from `reference/tokens.css` into the project's existing stylesheet (after the `@import` lines).
2. Update `components.json` to match the Tam DS config above (preserve existing `aliases` if they differ).
3. Run `pnpm add @hugeicons/react @hugeicons/core-free-icons @fontsource-variable/inter` if not already installed.

---

## Coding Assistant Mode (always active)

Apply these rules to every UI component, page, or template you generate.

### Design tokens

Use token-backed Tailwind classes. Never use raw color values or arbitrary `[]` colors.

| Role | Tailwind class | Notes |
|------|---------------|-------|
| Page background | `bg-background` | |
| Body text | `text-foreground` | |
| Primary action | `bg-primary` | Indigo/violet |
| Text on primary | `text-primary-foreground` | |
| Secondary surface | `bg-secondary` | |
| Secondary text | `text-secondary-foreground` | |
| Muted background | `bg-muted` | |
| Muted / hint text | `text-muted-foreground` | |
| Accent hover | `bg-accent` | |
| Card surface | `bg-card` | |
| Card text | `text-card-foreground` | |
| Destructive | `bg-destructive` / `text-destructive` | |
| Border | `border-border` | |
| Input border | `border-input` | |
| Focus ring | `ring-ring` | |
| Sidebar | `bg-sidebar` | |

Radius scale (base = `0.875rem`):
`rounded-sm` · `rounded-md` · `rounded-lg` · `rounded-xl` · `rounded-2xl` · `rounded-3xl` · `rounded-4xl`

Font: `font-sans` (Inter Variable). `font-heading` resolves to the same family.

### RTL — always enforce

The design system is RTL-first. Never use directional Tailwind classes:

| ❌ Never use | ✅ Always use |
|-------------|--------------|
| `ml-*` / `mr-*` | `ms-*` / `me-*` |
| `pl-*` / `pr-*` | `ps-*` / `pe-*` |
| `left-*` / `right-*` (inset) | `start-*` / `end-*` |
| `text-left` / `text-right` | `text-start` / `text-end` |
| `border-l-*` / `border-r-*` | `border-s-*` / `border-e-*` |
| `rounded-l-*` / `rounded-r-*` | `rounded-s-*` / `rounded-e-*` |
| `float-left` / `float-right` | `float-start` / `float-end` |
| `space-x-*` (directional) | Use `gap-*` with flex/grid instead |

### Icons

See `reference/icons.md` for full usage. Always use Hugeicons — never Lucide or others.

**React quick reference:**
```tsx
import { HugeiconsIcon } from '@hugeicons/react'
import { HomeIcon } from '@hugeicons/core-free-icons'

<HugeiconsIcon icon={HomeIcon} size={20} />
```

**HTML/template quick reference:**
```html
<i class="hgi-stroke hgi-home-01" aria-hidden="true"></i>
```

### Component primitives (React)

Shadcn components in this design system use **Base UI** primitives (`@base-ui/react`), not Radix UI (`@radix-ui`). When extending or wrapping components:
- Import from `@base-ui/react`, not `@radix-ui/react-*`
- Base UI uses `render` prop pattern for slot overrides (not `asChild`)

### Dark mode

Toggled via `.dark` class on `<html>`. Use `dark:` Tailwind variant for overrides.

### Code quality

- Use `cn()` from `@/lib/utils` (wraps `clsx` + `tailwind-merge`) for conditional classes
- Use `cva` from `class-variance-authority` for component variants
- Keep component files in `@/components/ui/` for shadcn components, `@/components/` for custom ones
