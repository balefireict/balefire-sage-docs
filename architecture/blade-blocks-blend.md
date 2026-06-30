# Blade + Blocks Blend

## Why PHP render callbacks (not React save functions)

- Markup lives entirely in PHP/Blade. Diffs cleanly in git.
- No block deprecation churn when attributes change.
- ACF fields, WP queries, conditional logic all run server-side.
- Same proven pattern nnnode already uses successfully.

## How a block renders

```
Gutenberg Editor (client)         Frontend (server)
───────────────────────           ──────────────────
editor.js                         block.json → render.php
  ├─ InspectorControls              ├─ maps $attributes → props
  ├─ attribute controls              └─ Renderer::render($props)
  └─ editor preview placeholder           └─ Blade view (bma::component)
                                            └─ Tailwind classes
                                                └─ @theme resolves tokens
```

The block's editor.js is ONLY the editing UI. No React save function, no client-side rendering. The server outputs the final HTML.

## When to use each layer

| Layer | Used for | Who places it |
|-------|----------|---------------|
| Blade only | Headers, footers, page layouts, structural wrappers | Developer (theme templates) |
| Blade + Block | CTAs, hero sections, feature grids, testimonials | Client (Gutenberg editor) |
| Blade + Shortcode | Backward compat for WPBakery migration | Legacy content |

**Same Blade file powers all three. Edit once, update everywhere.**

## Code Example: render.php

The render callback is thin glue — it maps block attributes to Blade props:

```php
// blocks/cta-banner/render.php
echo Renderer::render([
    'eyebrow'        => $attributes['eyebrow'] ?? '',
    'title'          => $attributes['title'] ?? '',
    'tone'           => $attributes['tone'] ?? 'primary',
    'primaryLabel'   => $attributes['primaryLabel'] ?? '',
    'primaryUrl'     => esc_url($attributes['primaryUrl'] ?? ''),
]);
```

## Code Example: editor.js

The editor UI uses React/JSX for InspectorControls. It includes a lightweight preview placeholder so the editor isn't blank — but the real markup comes from the server.

```jsx
<InspectorControls>
    <PanelBody title="Content">
        <TextControl
            label="Title"
            value={attributes.title || ''}
            onChange={(val) => setAttributes({ title: val })}
        />
    </PanelBody>
</InspectorControls>
```
