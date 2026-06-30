# Path A: WP Engine

Sage theme in standard `wp-content/themes/` layout. No Bedrock, no Trellis.

## How it works

1. Sage theme lives in `wp-content/themes/balefire-sage/`
2. Component monorepo is git-subtreed into the site repo (David Tours pattern)
3. `composer.json` selectively requires only the needed component packages
4. `vendor/` is committed to the repo
5. `dist/` (compiled assets) is committed to the repo
6. Deploy via existing GitPush workflow — no workflow changes

## Why this works on WPE

- No server-side Composer needed (vendor/ is committed)
- No Node on the server (dist/ is committed)
- No special wp-config setup
- Same GitPush deploy you already use for David Tours, Rockerbox, etc.
- WPBakery shortcodes continue working during transition (components register shortcodes too)

## The WPE + Packagist acquisition

WP Engine recently acquired Packagist. This signals they're moving toward Composer-native WordPress workflows. If WPE adds native Composer support to their platform, our architecture is forward-compatible — we can drop the committed vendor/ pattern and use server-side Composer without changing the component packages.
