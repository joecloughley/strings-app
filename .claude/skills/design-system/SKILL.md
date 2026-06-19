---
name: design-system
description: Design and styling guidelines for the Strings app frontend. Use when building or modifying UI — components, layouts, colors, typography, spacing, variable styling — to keep new work consistent with the existing shadcn/Tailwind v4 design system. Triggers include "build a UI", "style this", "make a component", "match the design", or any frontend visual work.
---

# Strings App — Design System

Design and styling conventions for the Strings app frontend. Follow these so new UI is visually and structurally consistent with what already exists.

## Stack

- **Next.js 15** (App Router, RSC) + **React 19**
- **Tailwind CSS v4** (CSS-first config via `@theme inline` in `globals.css` — there is **no** `tailwind.config.js`)
- **shadcn/ui** — "new-york" style, `neutral` base color, CSS variables enabled
- **Radix UI** primitives under the hood
- **lucide-react** for icons
- **sonner** for toasts, **vaul** for drawers
- `class-variance-authority` (cva) + `clsx` + `tailwind-merge` for variants

Key files:
- [globals.css](frontend/src/app/globals.css) — theme tokens, light/dark, Tailwind `@theme` mapping
- [embedded-variables.scss](frontend/src/styles/embedded-variables.scss) — base colors for string/conditional variables
- [COLOR-SYSTEM.md](frontend/src/styles/COLOR-SYSTEM.md) — how the variable color system works
- [style-guide/page.tsx](frontend/src/app/style-guide/page.tsx) — **live, rendered reference** for all text styles (visit `/style-guide`)
- [components.json](frontend/components.json) — shadcn config
- [lib/utils.ts](frontend/src/lib/utils.ts) — the `cn()` helper

## Core principles

1. **Use semantic tokens, never raw colors.** Reach for `bg-background`, `text-foreground`, `text-muted-foreground`, `border`, `bg-card`, `bg-primary`, `bg-accent`, `text-destructive` — not `bg-white`, `text-gray-500`, `bg-neutral-100`. These tokens auto-adapt to light/dark.
2. **Two domain color families** (`string-*` and `conditional-*`) are the app's signature. Use them for variable UI — see below.
3. **Compose with `cn()`.** Always merge classes through `cn()` from `@/lib/utils` so consumer overrides win.
4. **Build new components on shadcn primitives** in `src/components/ui/` before hand-rolling. Match their structure: `data-slot` attributes, cva variants, `React.ComponentProps<...>` typing.
5. **Mobile-first, responsive.** Use `sm:` / `md:` prefixes as in the existing layouts.

## Color tokens

Semantic tokens (defined in [globals.css](frontend/src/app/globals.css), in `oklch`, with `.dark` overrides):

| Purpose | Token classes |
|---|---|
| Page surface | `bg-background` / `text-foreground` |
| Cards, panels | `bg-card` / `text-card-foreground` |
| Popovers, menus | `bg-popover` / `text-popover-foreground` |
| Primary actions | `bg-primary` / `text-primary-foreground` |
| Secondary | `bg-secondary` / `text-secondary-foreground` |
| Subtle/muted | `bg-muted` / `text-muted-foreground` |
| Hover/accents | `bg-accent` / `text-accent-foreground` |
| Borders / inputs | `border-border` / `border-input` |
| Focus rings | `ring-ring` (shadcn uses `focus-visible:ring-ring/50 ring-[3px]`) |
| Danger | `bg-destructive` / `text-destructive` |

Radius scale is driven by `--radius: 0.625rem`: `rounded-sm/md/lg/xl` derive from it. Default to `rounded-md` for controls, `rounded-lg` for cards/panels.

### Variable colors (the app's signature system)

The app has two domain color families controlled from **two SCSS variables** in [embedded-variables.scss](frontend/src/styles/embedded-variables.scss):

```scss
$string-var-color: blue;        // ALL string variable colors
$conditional-var-color: gold;   // ALL conditional variable colors
```

These generate a 5-step scale exposed as Tailwind classes. **Use the semantic class names**, never hardcoded `purple-*` / `orange-*` / `blue-*`:

| Shade | Use | String class | Conditional class |
|---|---|---|---|
| 50 | very light bg, hover | `bg-string-50` | `bg-conditional-50` |
| 100 | light bg | `bg-string-100` | `bg-conditional-100` |
| 200 | borders | `border-string-200` | `border-conditional-200` |
| 600 | icons | `text-string-600` | `text-conditional-600` |
| 700 | text | `text-string-700` | `text-conditional-700` |

```tsx
// String variable styling
<div className="bg-string-50 text-string-700 border border-string-200">
  <FolderIcon className="text-string-600" />
</div>

// Conditional variable styling
<div className="bg-conditional-50 text-conditional-700 border border-conditional-200">
```

There's also a `pending-*` family (`bg-pending-50`, `bg-pending-200`, `text-pending`) for new/unsaved items.

**To re-theme the whole app**, edit the two `$*-var-color` SCSS variables — do not touch component classes. See [COLOR-SYSTEM.md](frontend/src/styles/COLOR-SYSTEM.md).

## Typography

Fonts (loaded in [layout.tsx](frontend/src/app/layout.tsx), exposed as CSS vars):
- **Geist Sans** (`--font-geist-sans`) — default UI font
- **Geist Mono** (`--font-geist-mono`, `font-mono`) — hashes, code, IDs
- **Courgette** (`font-courgette`) — brand/logo only

The canonical, rendered type scale lives at `/style-guide`. Match these exactly:

| Role | Classes |
|---|---|
| Page title (H1) | `text-4xl font-bold` |
| Section title (H2) | `text-2xl font-semibold` |
| Subsection (H3) | `text-xl font-semibold` |
| Card / panel title | `text-lg font-semibold` |
| Body | `text-base` |
| String content | `font-medium text-base leading-relaxed` |
| Description / helper | `text-sm text-muted-foreground` |
| Form label | `text-sm font-medium` |
| Uppercase section label | `text-[10px] text-muted-foreground uppercase tracking-wide` |
| Variable hash / ID | `text-sm text-muted-foreground` (or `font-mono`) |
| Badge / reference | `text-xs` |
| Logo | `text-3xl font-courgette tracking-wide text-primary` |

Font sizes: `text-xs` 12 · `text-sm` 14 · `text-base` 16 · `text-lg` 18 · `text-xl` 20 · `text-2xl` 24 · `text-3xl` 30 · `text-4xl` 36.
Weights in use: `font-normal` 400 · `font-medium` 500 · `font-semibold` 600 · `font-bold` 700.

## Status & feedback colors

These intentionally use literal Tailwind palette colors (not domain tokens):

- Success: `text-green-800` (light bg `bg-green-*`)
- Warning: `text-amber-800`
- Error / delete: `text-red-600`
- "New" badge: `text-xs px-1.5 py-0.5 rounded bg-amber-100 text-amber-700`
- Empty state: `text-sm text-muted-foreground`, often `italic`

Use `sonner` toasts for transient feedback (`<Toaster />` is mounted in the root layout).

## Spacing & layout

- Page container: `min-h-screen bg-background p-8` with an inner `max-w-4xl mx-auto`
- Vertical rhythm: `space-y-12` between sections, `space-y-6` within, `space-y-4` for tight groups
- Section headers: `text-2xl font-semibold border-b pb-2`
- Cards: `p-4 border rounded-lg bg-card`
- Icon size default is `size-4` (16px) inside buttons; controls are `h-9` (default), `h-8` (sm), `h-10` (lg)

## Components

Buttons ([button.tsx](frontend/src/components/ui/button.tsx)) define the cva pattern to follow for new variants:
- variants: `default`, `destructive`, `outline`, `secondary`, `ghost`, `link`
- sizes: `default`, `sm`, `lg`, `icon`
- Use `asChild` (Radix `Slot`) to render as a different element.

When creating a new UI primitive, mirror this shape: `cva` for variants, `data-slot` attribute, typed via `React.ComponentProps`, classes merged with `cn()`, exported alongside its `*Variants`.

Prefer composing existing primitives in `src/components/ui/`: `badge`, `card`, `dialog`, `dropdown-menu`, `input`, `label`, `popover`, `radio-group`, `select`, `sheet`, `switch`, `tabs`, `textarea`, `tooltip`, `multi-select`.

## Dark mode

Dark mode is handled by `.dark` class overrides on the tokens (next-themes available). Because you use semantic tokens, components get dark mode for free — **don't** add manual `dark:` color overrides except for the special cases shadcn already ships (e.g. `dark:bg-input/30`).

## Checklist before shipping UI

- [ ] Colors are semantic tokens or `string-*`/`conditional-*`, never raw hex/palette (except status colors)
- [ ] Classes merged via `cn()`
- [ ] Typography matches the `/style-guide` scale
- [ ] Built on a shadcn primitive where one exists
- [ ] Looks right in both light and dark mode
- [ ] Responsive (`sm:`/`md:`) where layout needs it
- [ ] Focus-visible states preserved on interactive elements
