---
name: tam-ds
description: Tam Design System. Use when setting up a new Tam project (pass "setup" as argument) or as an always-on coding assistant that enforces design tokens, RTL-safe Tailwind classes, Hugeicons, and Base UI conventions in all generated UI code.
metadata:
  author: mohamedhadia
  version: "1.2.0"
  argument-hint: "[setup]"
---

# Tam Design System

You are the Tam Design System assistant. Two modes:

- **Setup mode** — triggered by the `setup` argument, or when no `components.json` is detected. Installs the design system into the current project.
- **Coding assistant mode** — always active. Enforces DS rules in every piece of UI code you generate.

**Before doing anything:** read `tokens.css` and `icons.md` from the skill directory.

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
   - Adjust `"css"` to match the project's actual entry stylesheet.

3. **Write the CSS** — replace the contents of the file declared in `"tailwind.css"` with:
   ```css
   @import "tailwindcss";
   @import "tw-animate-css";
   @import "shadcn/tailwind.css";
   @import "@fontsource-variable/inter";
   ```
   Then append the full content of `tokens.css`.

4. **Install packages:**
   ```bash
   pnpm add @hugeicons/react @hugeicons/core-free-icons @fontsource-variable/inter tw-animate-css
   ```

5. **Verify** by adding the button component:
   ```bash
   pnpm dlx shadcn@latest add button
   ```
   The generated `button.tsx` must import from `@base-ui/react/button`, not `@radix-ui`. If it imports Radix, re-check `components.json` — `"style": "base-rhea"` is required.

### Step 2B — Rails / Django (server-side templates)

1. **Create the token stylesheet** — write the content of `tokens.css` to:
   - Rails: `app/assets/stylesheets/tam-ds.css`
   - Django: `static/css/tam-ds.css`

2. **Add Inter font** in your base layout `<head>`:
   ```html
   <link rel="preconnect" href="https://fonts.googleapis.com">
   <link href="https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,100..900;1,14..32,100..900&display=swap" rel="stylesheet">
   ```
   Then in your base CSS: `body { font-family: 'Inter', sans-serif; }`

3. **Add Hugeicons CDN** in your base layout `<head>` — see `icons.md` for the snippet.

4. **Configure Tailwind** to scan templates and import the token file:
   ```css
   @import "tailwindcss";
   @import "./tam-ds.css";
   ```

### Step 3 — Existing shadcn project

1. Replace the token block in the project CSS with the content of `tokens.css` (keep existing `@import` lines above it).
2. Ensure `components.json` matches the Tam DS config in Step 2A (preserve existing `aliases` if they differ).
3. Run `pnpm add @hugeicons/react @hugeicons/core-free-icons @fontsource-variable/inter` if not already installed.

---

## Coding Assistant Mode (always active)

Apply these rules to every UI component, page, or template you generate.

### Design Tokens

Use token-backed Tailwind classes. Never use raw color values or arbitrary `[]` colors.

| Role | Tailwind class |
|------|---------------|
| Page background | `bg-background` |
| Body text | `text-foreground` |
| Primary action | `bg-primary` |
| Text on primary | `text-primary-foreground` |
| Secondary surface | `bg-secondary` |
| Secondary text | `text-secondary-foreground` |
| Muted background | `bg-muted` |
| Muted / hint text | `text-muted-foreground` |
| Accent hover | `bg-accent` |
| Card surface | `bg-card` |
| Card text | `text-card-foreground` |
| Destructive | `bg-destructive` / `text-destructive` |
| Border | `border-border` |
| Input border | `border-input` |
| Focus ring | `ring-ring` |
| Sidebar | `bg-sidebar` |

Radius scale (base = `0.875rem`):
`rounded-sm` · `rounded-md` · `rounded-lg` · `rounded-xl` · `rounded-2xl` · `rounded-3xl` · `rounded-4xl`

Font: `font-sans` (Inter Variable). `font-heading` resolves to the same family.

### RTL — always enforce

Never use directional Tailwind classes. The design system is RTL-first:

| ❌ Never use | ✅ Always use |
|-------------|--------------|
| `ml-*` / `mr-*` | `ms-*` / `me-*` |
| `pl-*` / `pr-*` | `ps-*` / `pe-*` |
| `left-*` / `right-*` (inset) | `start-*` / `end-*` |
| `text-left` / `text-right` | `text-start` / `text-end` |
| `border-l-*` / `border-r-*` | `border-s-*` / `border-e-*` |
| `rounded-l-*` / `rounded-r-*` | `rounded-s-*` / `rounded-e-*` |
| `float-left` / `float-right` | `float-start` / `float-end` |
| `space-x-*` | Use `gap-*` with flex/grid instead |

### Component Primitives (React)

Shadcn components use **Base UI** primitives (`@base-ui/react`), not Radix UI. When extending or wrapping components:
- Import from `@base-ui/react`, not `@radix-ui/react-*`
- Base UI uses the `render` prop for slot overrides, not `asChild`

### Icons

See `icons.md` for full usage. Always use Hugeicons — never Lucide or others.

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

### Dark Mode

Toggled via `.dark` class on `<html>`. Use `dark:` Tailwind variant for overrides.

### Code Quality

- Use `cn()` from `@/lib/utils` for conditional classes
- Use `cva` from `class-variance-authority` for component variants
- Keep shadcn components in `@/components/ui/`, custom ones in `@/components/`
