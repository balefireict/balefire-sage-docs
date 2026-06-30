# Reference Implementation

The `balefire-sage-theme` is a reference Sage 10 consumer theme that proves the full end-to-end pipeline.

## What it proves

1. A Sage theme requires components from the monorepo via Composer
2. Components auto-register their Gutenberg blocks
3. Blade views are namespaced as `bma::` and available in theme templates
4. Tailwind v4 `@theme` maps brand tokens to the semantic names components use
5. Bud compiles both theme assets AND vendor block editor JS

## End-to-end flow

```
1. Sage theme requires components from monorepo via Composer
2. Component bootstrap.php fires on init → registers Gutenberg block
3. Block render.php maps attrs → Renderer::render() → Blade view
4. Blade view uses semantic Tailwind tokens (bg-primary, font-headline)
5. Tailwind v4 @theme resolves those tokens to site brand colors
6. Bud compiles theme CSS + auto-discovers vendor editor.js files
7. Client adds CTA Banner block in Gutenberg
8. Frontend renders server-side via the Blade component
```

## Two ways to use a component

### Direct Blade include (developer-placed)

```blade
<x-bma::cta-banner
    :eyebrow="'Developer-placed'"
    :title="'Hard-coded in the template'"
    :tone="'dark'"
    :primary-label="'Learn More'"
    :primary-url="'/about/'"
/>
```

### Gutenberg block (client-placed)

Client opens the page in the block editor, adds the "CTA Banner" block under the "Balefire" category, and sets props via InspectorControls. The block's render.php calls the same Blade view.

Both render the exact same Blade component file.
