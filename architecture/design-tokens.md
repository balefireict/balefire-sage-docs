# Design Tokens

Components are **brand-agnostic**. They use semantic Tailwind classes (`bg-primary`, `text-accent`, `font-headline`). Each consumer theme defines what those names resolve to via a Tailwind v4 `@theme` block.

**Change the brand = change theme.css values. Components never change.**

## How it works

Three layers, left to right. Only the leftmost column changes per site.

```
theme.css (per-site)              @theme mapping              component Blade
────────────────────────          ────────────────            ──────────────
--bfa-blue: #00338f       →      --color-primary       →     bg-primary
--bfa-red: #c8102e        →      --color-accent        →     text-accent
--bfa-font-heading: "..."  →     --font-headline       →     font-headline
```

The component says "use the primary color." The theme says "primary is blue." This separation means the same CTA Banner block looks correct on every client site without touching component code.

## Required tokens (the contract)

### Colors

| Tailwind utility | @theme variable | Usage |
|-----------------|-----------------|-------|
| `bg-primary` | `--color-primary` | Primary brand color (buttons, tone: primary) |
| `bg-primary-dark` | `--color-primary-dark` | Hover states |
| `bg-secondary` | `--color-secondary` | Secondary brand color |
| `bg-accent` | `--color-accent` | Accent/CTA highlight |
| `bg-accent-dark` | `--color-accent-dark` | Accent hover |
| `bg-dark` | `--color-dark` | Dark backgrounds |
| `text-dark` | `--color-dark` | Body text on light |
| `bg-surface` | `--color-surface` | Light surface/card bg |
| `bg-muted` | `--color-muted` | Subtle backgrounds |
| `text-muted` | `--color-muted` | Secondary text |

### Typography

| Tailwind utility | @theme variable | Usage |
|-----------------|-----------------|-------|
| `font-headline` | `--font-headline` | Heading font family |
| `font-body` | `--font-body` | Body text font family |

### Optional

| Tailwind utility | @theme variable | Default | Usage |
|-----------------|-----------------|---------|-------|
| `shadow-card` | `--shadow-card` | `0 6px 20px rgb(0 0 0 / 0.08)` | Card elevation |
| `rounded-card` | `--radius-card` | `2rem` | Card/banner radius |

## Per-site setup (the 30-second rebrand)

The consumer Sage theme provides two CSS files:

### 1. theme.css — brand variables (only per-site file)

```css
/* David Tours example */
:root {
    --bfa-blue: #00338f;
    --bfa-blue-dark: #00186c;
    --bfa-red: #c8102e;
    --bfa-red-dark: #84081c;
    --bfa-slate: #6f779d;
    --bfa-black: #1a1a1a;
    --bfa-font-heading: "DM Serif Display", Georgia, serif;
    --bfa-font-body: "Montserrat", system-ui, sans-serif;
}
```

### 2. app.css — @theme mapping (boilerplate, copy once)

```css
@import "tailwindcss";

@theme {
    --color-primary: var(--bfa-blue);
    --color-primary-dark: var(--bfa-blue-dark);
    --color-secondary: var(--bfa-red);
    --color-accent: var(--bfa-red);
    --color-accent-dark: var(--bfa-red-dark);
    --color-dark: var(--bfa-black);
    --color-surface: #f4f4f4;
    --color-muted: var(--bfa-slate);
    --font-headline: var(--bfa-font-heading);
    --font-body: var(--bfa-font-body);
}
```

New client site: swap hex values in theme.css, rebuild. Every component across the site updates.
