# The Monorepo

All components live in a single git repo (`balefire-sage-components`) as sub-packages under `packages/`. This mirrors the existing balefire-components monorepo proven on David Tours.

## Structure

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

## Selective Installation

Sites only install the components they actually use. The monorepo is the single source; consumers pick what they need.

### Local dev (monorepo itself)

Path repos with symlinks. Edit a package, changes reflect instantly:

```json
{
    "repositories": [
        {
            "type": "path",
            "url": "./packages/*",
            "options": { "symlink": true }
        }
    ]
}
```

### WPE sites — git subtree + committed vendor/

The consumer site git-subtrees the monorepo into the site repo (David Tours pattern), then selectively requires only the packages it needs:

```json
{
    "repositories": [
        {
            "type": "path",
            "url": "wp-content/themes/balefire-sage/components/packages/*",
            "options": { "symlink": false }
        }
    ],
    "require": {
        "balefireict/component-cta-banner": "dev-main",
        "balefireict/component-hero": "dev-main"
    }
}
```

`vendor/` is committed and deployed via GitPush. Only required packages land in vendor/. No server-side Composer. Proven on David Tours.

### VPS / Trellis sites — Composer on deploy

Composer runs on deploy (Trellis), pulling from Forgejo. Fully selective, no subtree, no committed vendor/.

## Why a monorepo (not one repo per component)

- One PR can touch multiple components simultaneously
- Atomic versioning — all components share a coherent release history
- Simpler CI — one pipeline lints, tests, and publishes all packages
- Fewer repos to manage, clone, and keep in sync
- Proven pattern: we already run balefire-components this way
