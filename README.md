# Balefire Sage Component Library — Strategy & Architecture

---

## Executive Summary

Standardize the agency on **Sage 10 + Tailwind CSS** backed by a shared **Gutenberg block + Blade component library** distributed via Composer. Every new client site pulls from the same pool of reusable sections. Build a section once, ship it everywhere. WPBakery sites stay untouched.

Accu-shot.com is already being built on Sage, giving us a running start on the component catalog.

---

## The Problem

Every new WPBakery site re-invents the same building blocks from scratch. Buttons, cards, CTAs, hero sections, feature grids, testimonials, sliders. Code is copy-pasted between themes, diverges over time, and improvements on one site never make it back to others.

We proved the Composer distribution model on David Tours (17+ component packages). We proved the Gutenberg + Tailwind block model on nnnode (17 blocks, PHP render callbacks). This plan unifies both into a shared library every new Sage site consumes.

---

## Sage vs EtchWP

### Sage 10 — Selected

Sage is an open-source WordPress theme framework by Roots (MIT licensed, 10+ years development, large community). It uses Blade templates, Tailwind CSS, and a modern build pipeline (Bud).

- **Native Tailwind CSS.** No framework lock-in, no proprietary CSS system.
- **Components are real files** (PHP, Blade, JS). Version controlled, diffable, distributable via Composer packages.
- **Zero vendor lock-in.** MIT licensed. If Roots disappeared tomorrow, every site keeps working.
- **Skills transfer** beyond WordPress. Blade and Tailwind are standard web dev skills.
- **We already have a live Sage build** (accu-shot.com) to extract from.

### EtchWP — Not Selected

Etch (by Digital Gravy) is a visual builder that outputs clean code and syncs to Gutenberg blocks. Genuinely impressive tool, but the wrong fit for us:

- Uses **Automatic.css (ACSS)**, not Tailwind. Different CSS methodology.
- Components are **JSON blobs in the WordPress database**, not version-controlled files. Cannot wire to Composer.
- **Proprietary and commercial.** Agency and every client site locked to one vendor's roadmap.
- **No migration path out.** Components are Etch-specific JSON.
- Still **v1.5.x** with an explicitly experimental API surface.

---

## The Architecture: Blade + Blocks Blend

Each component is built on a three-layer model.

### Layer 1: Blade Component (the markup)

The single source of truth for the component's HTML and Tailwind classes. A Blade file with typed props, slots, conditionals, and loops.

```blade
@props([
    'eyebrow' => '',
    'title' => '',
    'tone' => 'primary',
])

<section {{ $attributes->class(['rounded-card px-6 py-8', ...]) }}>
    @unless(empty($title))
        <h2 class="text-3xl font-headline">{{ $title }}</h2>
    @endunless
</section>
```

### Layer 2: Gutenberg Block (client editing UI)

A thin wrapper that maps block attributes to Blade component props. The block editor is just the editing interface — the frontend is pure server-rendered HTML.

```php
// render.php — just glue
echo Renderer::render([
    'eyebrow' => $attributes['eyebrow'] ?? '',
    'title'   => $attributes['title'] ?? '',
]);
```

### Layer 3: Shortcode (optional backward compat)

Same Blade view, different entry point. For WPBakery sites during migration.

### Why PHP render callbacks (not React save functions)?

- Markup lives entirely in PHP/Blade — diffs cleanly in git
- No block deprecation churn when attributes change
- ACF fields, WP queries, conditional logic all run server-side
- Same proven pattern nnnode already uses

### When to use each layer

| Layer | Used for | Who places it |
|-------|----------|---------------|
| Blade only | Headers, footers, page layouts | Developer (theme templates) |
| Blade + Block | CTAs, hero sections, feature grids | Client (Gutenberg editor) |
| Blade + Shortcode | Backward compat for WPBakery | Legacy content |

**Same Blade file powers all three. Edit once, update everywhere.**

---

## The Monorepo

All components live in a single git repo (`balefire-sage-components`) as sub-packages under `packages/`. This mirrors the existing balefire-components monorepo proven on David Tours.

### Structure

```
balefire-sage-components/
  composer.json              monorepo root (path repos, symlinks)
  packages/
    component-cta-banner/
      composer.json          sub-package manifest
      src/bootstrap.php      registers block + shortcode on init
      src/Renderer.php       delegates to Blade view
      blocks/cta-banner/
        block.json           Gutenberg metadata
        render.php           block render callback (attrs → Blade)
        editor.js            InspectorControls + editor preview
      resources/views/components/
        cta-banner.blade.php the markup (single source of truth)
    component-hero/
      ...
```

### Selective installation

Sites only install the components they use.

**Local dev (monorepo itself):** Path repos with symlinks — edit a package, changes reflect instantly.

**WPE sites:** Git subtree the monorepo into the site repo (David Tours pattern), selectively require packages, commit `vendor/`. No server-side Composer needed.

**VPS / Trellis sites:** Composer runs on deploy, pulling from Forgejo. Fully selective, no subtree.

---

## Design Tokens

Components are **brand-agnostic**. They use semantic Tailwind classes (`bg-primary`, `text-accent`, `font-headline`). Each consumer theme maps these to real brand values.

**Change the brand = change theme.css values. Components never change.**

```
theme.css (per-site)              @theme mapping              component Blade
────────────────────────          ────────────────            ──────────────
--bfa-blue: #00338f       →      --color-primary       →     bg-primary
--bfa-red: #c8102e        →      --color-accent        →     text-accent
--bfa-font-heading: "..."  →     --font-headline       →     font-headline
```

The 30-second rebrand: change 6 hex values in theme.css, rebuild. Every component across the entire site updates.

---

## Deployment

### Path A — WP Engine (existing clients)

- Sage theme in standard `wp-content/themes/` layout
- `vendor/` committed to repo (no server-side Composer)
- `dist/` (compiled assets) committed to repo
- Deploy via existing GitPush workflow
- No Bedrock, no Trellis, no workflow changes

### Path B — VPS (new projects)

- Full Roots stack: Bedrock + Sage + Trellis
- Atomic deploys (symlink swap, instant rollback)
- Ansible-provisioned server (nginx, PHP-FPM, MariaDB, Redis)
- Composer runs on deploy
- Dedicated server projects where WordPress is the whole box

Both paths consume the same component library with zero modifications.

---

## Repository Strategy

Self-hosted **Forgejo** on existing Hetzner VPS. Public repos at `git.balefireagency.com`.

- Component library monorepo: public (no secrets)
- Client site repos: private on same Forgejo instance
- WPE recently acquired Packagist, signaling a move toward Composer-native deploys. Our architecture is forward-compatible if WPE adds native Composer support.

---

## Timeline

| Phase | Weeks | Deliverable |
|-------|-------|-------------|
| Foundation | 1-2 | Forgejo setup, monorepo scaffold, 3-5 components extracted from accu-shot |
| First Sage Site | 3-5 | Finish accu-shot on Sage + Tailwind, document workflow |
| Library Expansion | 6-8 | Full component catalog (20+ blocks), team training |
| Full Adoption | 9+ | All new projects on Sage + Tailwind |

---

## Costs

| Item | Cost |
|------|------|
| Forgejo VPS (Hetzner) | $0 (existing server) |
| Sage / Tailwind | Free (MIT / open source) |
| Team training | 2-3 weeks per developer |

**Hard cost: $0.** The investment is team time.

---

## Risks

| Risk | Mitigation |
|------|------------|
| Team adoption | Prove on accu-shot first, then roll out with training |
| Library fragmentation | One maintainer owns the library, PRs from devs |
| Sage breaking changes | Sage 10 stable, Roots 10yr track record |
| Forgejo downtime | Commit vendor/ on WPE; VPS uses CI |

---

## The Ask

1. Green-light Forgejo setup on Hetzner (server already running)
2. Approve 2-week Phase 1 sprint
3. Confirm accu-shot.com as the reference implementation
4. Identify the second Sage project for Phase 2
