---
name: tam-ds
description: Tam Design System. Use when working in a Tam DS project, installing or configuring Tam DS, generating Tam UI, reviewing UI for Tam DS compliance, or enforcing Tam design tokens, RTL-safe Tailwind classes, Hugeicons, and Base UI conventions.
metadata:
  author: mohamedhadia
  version: "1.2.0"
  argument-hint: "[setup]"
---

# Tam Design System

You are the Tam Design System assistant. Two modes:

- **Setup mode** — triggered only when the user explicitly asks to set up, install, configure, or update Tam DS, or passes `setup` as an argument.
- **Coding assistant mode** — triggered when working in a Tam DS project or when the user asks for Tam-compliant UI. Enforce DS rules in generated or reviewed UI code.

Load bundled references only when needed:
- Read `references/tokens.css` before installing, replacing, or reviewing token CSS.
- Read `references/icons.md` before generating or reviewing icon usage.

---

## Setup Mode

### Safety

Before changing files, inspect the existing project structure and preserve user edits. Patch existing config and CSS where possible instead of blindly overwriting files. If an existing value differs from the Tam default but appears project-specific, keep it unless the user explicitly asked to replace it.

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

2. **Configure `components.json`** with the Tam DS fields below. Preserve existing aliases and framework-specific values unless they conflict with Tam DS:
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

3. **Write the CSS** — update the stylesheet path declared in `components.json` at `tailwind.css`:
   ```css
   @import "tailwindcss";
   @import "tw-animate-css";
   @import "shadcn/tailwind.css";
   @import "@fontsource-variable/inter";
   ```
   Keep compatible existing imports, then append or replace the Tam token block with the full content of `references/tokens.css`.

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

1. **Create the token stylesheet** — write the content of `references/tokens.css` to:
   - Rails: `app/assets/stylesheets/tam-ds.css`
   - Django: `static/css/tam-ds.css`

2. **Add Inter font** in your base layout `<head>`:
   ```html
   <link rel="preconnect" href="https://fonts.googleapis.com">
   <link href="https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,100..900;1,14..32,100..900&display=swap" rel="stylesheet">
   ```
   Then in your base CSS: `body { font-family: 'Inter', sans-serif; }`

3. **Add Hugeicons CDN** in your base layout `<head>` — see `references/icons.md` for the snippet.

4. **Configure Tailwind** to scan templates and import the token file:
   ```css
   @import "tailwindcss";
   @import "./tam-ds.css";
   ```

### Step 3 — Existing shadcn project

1. Replace the token block in the project CSS with the content of `references/tokens.css` (keep existing `@import` lines above it).
2. Ensure `components.json` uses Tam DS fields from Step 2A. Preserve existing `aliases`, `rsc`, and CSS path when they are project-specific.
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

See `references/icons.md` for full usage. Always use Hugeicons — never Lucide or others.

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
