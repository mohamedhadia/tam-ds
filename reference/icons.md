# Hugeicons Usage

Always use Hugeicons. Never use Lucide, Heroicons, or any other icon library.
Browse the full icon set at https://hugeicons.com.

---

## React / Next.js / Vite

**Packages:** `@hugeicons/react` + `@hugeicons/core-free-icons`

```tsx
import { HugeiconsIcon } from '@hugeicons/react'
import { HomeIcon, User01Icon, Settings01Icon, ArrowRight01Icon } from '@hugeicons/core-free-icons'

// Basic
<HugeiconsIcon icon={HomeIcon} size={20} />

// With explicit color (defaults to currentColor)
<HugeiconsIcon icon={User01Icon} size={24} color="currentColor" />

// Thinner stroke
<HugeiconsIcon icon={Settings01Icon} size={20} strokeWidth={1.5} />

// Inside a button (inherits text color via currentColor)
<button className="flex items-center gap-2">
  Continue <HugeiconsIcon icon={ArrowRight01Icon} size={16} />
</button>
```

**Icon naming convention:** `{PascalCaseName}Icon` from `@hugeicons/core-free-icons`.
Search hugeicons.com for the icon name, then append `Icon`. If there are numbered variants
(e.g. `Home01Icon`, `Home02Icon`) pick the lowest number as the default unless a specific
variant fits better.

**Install:**
```bash
pnpm add @hugeicons/react @hugeicons/core-free-icons
```

---

## HTML / Rails ERB / Django Templates

Add once to your base layout `<head>`:

```html
<link rel="stylesheet" href="https://cdn.hugeicons.com/font/hgi-stroke-rounded.css" />
```

Then use in templates:

```html
<!-- Decorative (hidden from screen readers) -->
<i class="hgi-stroke hgi-home-01" aria-hidden="true"></i>

<!-- With size via Tailwind -->
<i class="hgi-stroke hgi-user text-xl text-primary" aria-hidden="true"></i>

<!-- Standalone icon with accessible label -->
<button aria-label="Settings">
  <i class="hgi-stroke hgi-settings-01 text-lg" aria-hidden="true"></i>
</button>
```

**Icon class format:** `hgi-stroke hgi-{kebab-case-name}`

To find the class name: search hugeicons.com → convert the icon name to kebab-case → prefix with `hgi-`.
Examples: `Home 01` → `hgi-home-01`, `User` → `hgi-user`, `Arrow Right 01` → `hgi-arrow-right-01`.

**Sizing:** use `font-size` CSS or Tailwind text size classes (`text-sm`, `text-lg`, `text-xl`, etc.)
**Color:** inherits `color` by default; override with Tailwind text color classes (`text-primary`, `text-muted-foreground`, etc.)
