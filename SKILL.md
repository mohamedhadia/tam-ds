---
name: tam-ds
description: Tam Design System. Use when setting up a new Tam project (pass "setup" as argument) or as an always-on coding assistant that enforces design tokens, RTL-safe Tailwind classes, Hugeicons, and Base UI conventions in all generated UI code.
metadata:
  author: mohamedhadia
  version: "1.1.0"
  argument-hint: "[setup]"
---

# Tam Design System

You are the Tam Design System assistant. Two modes:

- **Setup mode** — triggered by the `setup` argument, or when no `components.json` is detected. Installs the design system into the current project.
- **Coding assistant mode** — always active. Enforces DS rules in every piece of UI code you generate.

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
   Then append the full **Token CSS** block from the Tokens section below.

4. **Install packages:**
   ```bash
   pnpm add @hugeicons/react @hugeicons/core-free-icons @fontsource-variable/inter tw-animate-css
   ```

5. **Verify** by adding the button component:
   ```bash
   pnpm dlx shadcn@latest add button
   ```
   The generated `button.tsx` must import from `@base-ui/react/button`, not `@radix-ui`. If it imports Radix, re-check `components.json` — `style: "base-rhea"` is required.

### Step 2B — Rails / Django (server-side templates)

1. **Create the token stylesheet** — write the **Token CSS** block (from the Tokens section below) to:
   - Rails: `app/assets/stylesheets/tam-ds.css`
   - Django: `static/css/tam-ds.css`

2. **Add Inter font** in your base layout `<head>`:
   ```html
   <link rel="preconnect" href="https://fonts.googleapis.com">
   <link href="https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,100..900;1,14..32,100..900&display=swap" rel="stylesheet">
   ```
   Then in your base CSS: `body { font-family: 'Inter', sans-serif; }`

3. **Add Hugeicons CDN** in your base layout `<head>`:
   ```html
   <link rel="stylesheet" href="https://cdn.hugeicons.com/font/hgi-stroke-rounded.css" />
   ```

4. **Configure Tailwind** to scan templates and import the token file:
   ```css
   @import "tailwindcss";
   @import "./tam-ds.css";
   ```

5. **Import** `tam-ds.css` in your main stylesheet.

### Step 3 — Existing shadcn project

1. Replace the token block in the project's CSS with the **Token CSS** below (keep existing `@import` lines above it).
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

### Dark Mode

Toggled via `.dark` class on `<html>`. Use `dark:` Tailwind variant for overrides.

### Code Quality

- Use `cn()` from `@/lib/utils` for conditional classes
- Use `cva` from `class-variance-authority` for component variants
- Keep shadcn components in `@/components/ui/`, custom ones in `@/components/`

---

## Icons

Always use Hugeicons. Never use Lucide, Heroicons, or any other icon library.
Browse the full icon set at https://hugeicons.com.

### React / Next.js / Vite

**Packages:** `@hugeicons/react` + `@hugeicons/core-free-icons`

```tsx
import { HugeiconsIcon } from '@hugeicons/react'
import { HomeIcon, User01Icon, Settings01Icon, ArrowRight01Icon } from '@hugeicons/core-free-icons'

// Basic
<HugeiconsIcon icon={HomeIcon} size={20} />

// With stroke width
<HugeiconsIcon icon={Settings01Icon} size={20} strokeWidth={1.5} />

// Inside a button (inherits text color via currentColor)
<button className="flex items-center gap-2">
  Continue <HugeiconsIcon icon={ArrowRight01Icon} size={16} />
</button>
```

**Icon naming:** `{PascalCaseName}Icon` from `@hugeicons/core-free-icons`. Search hugeicons.com for the name, append `Icon`. For numbered variants (`Home01Icon`, `Home02Icon`) use the lowest number unless a specific variant fits better.

**Props:** `icon` (required), `size`, `strokeWidth`, `absoluteStrokeWidth`, `color`, `primaryColor`, `secondaryColor`

### HTML / Rails ERB / Django Templates

Add once to base layout `<head>`:
```html
<link rel="stylesheet" href="https://cdn.hugeicons.com/font/hgi-stroke-rounded.css" />
```

Use in templates:
```html
<!-- Decorative -->
<i class="hgi-stroke hgi-home-01" aria-hidden="true"></i>

<!-- With Tailwind sizing and color -->
<i class="hgi-stroke hgi-user text-xl text-primary" aria-hidden="true"></i>

<!-- Accessible standalone icon -->
<button aria-label="Settings">
  <i class="hgi-stroke hgi-settings-01 text-lg" aria-hidden="true"></i>
</button>
```

**Class format:** `hgi-stroke hgi-{kebab-case-name}`
Convert icon name to kebab-case: `Home 01` → `hgi-home-01`, `Arrow Right 01` → `hgi-arrow-right-01`
Size via `font-size` or Tailwind text classes. Color inherits `currentColor` by default.

---

## Token CSS

Write this block into the project CSS file (after bundler `@import` lines for React stacks; as the full file content for Rails/Django):

```css
@custom-variant dark (&:is(.dark *));

@theme inline {
    --font-heading: var(--font-sans);
    --font-sans: 'Inter Variable', sans-serif;
    --color-sidebar-ring: var(--sidebar-ring);
    --color-sidebar-border: var(--sidebar-border);
    --color-sidebar-accent-foreground: var(--sidebar-accent-foreground);
    --color-sidebar-accent: var(--sidebar-accent);
    --color-sidebar-primary-foreground: var(--sidebar-primary-foreground);
    --color-sidebar-primary: var(--sidebar-primary);
    --color-sidebar-foreground: var(--sidebar-foreground);
    --color-sidebar: var(--sidebar);
    --color-chart-5: var(--chart-5);
    --color-chart-4: var(--chart-4);
    --color-chart-3: var(--chart-3);
    --color-chart-2: var(--chart-2);
    --color-chart-1: var(--chart-1);
    --color-ring: var(--ring);
    --color-input: var(--input);
    --color-border: var(--border);
    --color-destructive: var(--destructive);
    --color-accent-foreground: var(--accent-foreground);
    --color-accent: var(--accent);
    --color-muted-foreground: var(--muted-foreground);
    --color-muted: var(--muted);
    --color-secondary-foreground: var(--secondary-foreground);
    --color-secondary: var(--secondary);
    --color-primary-foreground: var(--primary-foreground);
    --color-primary: var(--primary);
    --color-popover-foreground: var(--popover-foreground);
    --color-popover: var(--popover);
    --color-card-foreground: var(--card-foreground);
    --color-card: var(--card);
    --color-foreground: var(--foreground);
    --color-background: var(--background);
    --radius-sm: calc(var(--radius) * 0.6);
    --radius-md: calc(var(--radius) * 0.8);
    --radius-lg: var(--radius);
    --radius-xl: calc(var(--radius) * 1.4);
    --radius-2xl: calc(var(--radius) * 1.8);
    --radius-3xl: calc(var(--radius) * 2.2);
    --radius-4xl: calc(var(--radius) * 2.6);
}

:root {
    --background: oklch(1 0 0);
    --foreground: oklch(0.141 0.005 285.823);
    --card: oklch(1 0 0);
    --card-foreground: oklch(0.141 0.005 285.823);
    --popover: oklch(1 0 0);
    --popover-foreground: oklch(0.141 0.005 285.823);
    --primary: oklch(0.457 0.24 277.023);
    --primary-foreground: oklch(0.962 0.018 272.314);
    --secondary: oklch(0.967 0.001 286.375);
    --secondary-foreground: oklch(0.21 0.006 285.885);
    --muted: oklch(0.967 0.001 286.375);
    --muted-foreground: oklch(0.552 0.016 285.938);
    --accent: oklch(0.967 0.001 286.375);
    --accent-foreground: oklch(0.21 0.006 285.885);
    --destructive: oklch(0.577 0.245 27.325);
    --border: oklch(0.92 0.004 286.32);
    --input: oklch(0.92 0.004 286.32);
    --ring: oklch(0.705 0.015 286.067);
    --chart-1: oklch(0.871 0.006 286.286);
    --chart-2: oklch(0.552 0.016 285.938);
    --chart-3: oklch(0.442 0.017 285.786);
    --chart-4: oklch(0.37 0.013 285.805);
    --chart-5: oklch(0.274 0.006 286.033);
    --radius: 0.875rem;
    --sidebar: oklch(0.985 0 0);
    --sidebar-foreground: oklch(0.141 0.005 285.823);
    --sidebar-primary: oklch(0.511 0.262 276.966);
    --sidebar-primary-foreground: oklch(0.962 0.018 272.314);
    --sidebar-accent: oklch(0.967 0.001 286.375);
    --sidebar-accent-foreground: oklch(0.21 0.006 285.885);
    --sidebar-border: oklch(0.92 0.004 286.32);
    --sidebar-ring: oklch(0.705 0.015 286.067);
}

.dark {
    --background: oklch(0.141 0.005 285.823);
    --foreground: oklch(0.985 0 0);
    --card: oklch(0.21 0.006 285.885);
    --card-foreground: oklch(0.985 0 0);
    --popover: oklch(0.21 0.006 285.885);
    --popover-foreground: oklch(0.985 0 0);
    --primary: oklch(0.398 0.195 277.366);
    --primary-foreground: oklch(0.962 0.018 272.314);
    --secondary: oklch(0.274 0.006 286.033);
    --secondary-foreground: oklch(0.985 0 0);
    --muted: oklch(0.274 0.006 286.033);
    --muted-foreground: oklch(0.705 0.015 286.067);
    --accent: oklch(0.274 0.006 286.033);
    --accent-foreground: oklch(0.985 0 0);
    --destructive: oklch(0.704 0.191 22.216);
    --border: oklch(1 0 0 / 10%);
    --input: oklch(1 0 0 / 15%);
    --ring: oklch(0.552 0.016 285.938);
    --chart-1: oklch(0.871 0.006 286.286);
    --chart-2: oklch(0.552 0.016 285.938);
    --chart-3: oklch(0.442 0.017 285.786);
    --chart-4: oklch(0.37 0.013 285.805);
    --chart-5: oklch(0.274 0.006 286.033);
    --sidebar: oklch(0.21 0.006 285.885);
    --sidebar-foreground: oklch(0.985 0 0);
    --sidebar-primary: oklch(0.585 0.233 277.117);
    --sidebar-primary-foreground: oklch(0.962 0.018 272.314);
    --sidebar-accent: oklch(0.274 0.006 286.033);
    --sidebar-accent-foreground: oklch(0.985 0 0);
    --sidebar-border: oklch(1 0 0 / 10%);
    --sidebar-ring: oklch(0.552 0.016 285.938);
}

@layer base {
    * {
        @apply border-border outline-ring/50;
    }
    body {
        @apply bg-background text-foreground;
    }
    html {
        @apply font-sans;
    }
}
```
