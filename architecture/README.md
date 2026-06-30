# Architecture

The component library is built on three core principles:

1. **Single source of truth for markup** — one Blade file per component
2. **Multiple entry points** — Gutenberg block, direct Blade include, shortcode
3. **Brand-agnostic** — components use semantic tokens, never raw colors

## The Three-Layer Model

Each component follows a three-layer architecture:

| Layer | File | Purpose |
|-------|------|---------|
| Blade component | `resources/views/components/*.blade.php` | Single source of truth for markup + Tailwind classes |
| Gutenberg block | `blocks/*/block.json`, `render.php`, `editor.js` | Client editing UI in the block editor |
| Shortcode | `src/bootstrap.php` | Optional WPBakery / legacy compat |

The Blade file is the only place markup lives. The block's `render.php` maps attributes to Blade props and echoes the rendered view. The editor.js provides the InspectorControls UI and a lightweight preview placeholder.
