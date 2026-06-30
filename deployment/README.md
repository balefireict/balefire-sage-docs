# Deployment

Two deployment paths, both consuming the same component library.

| Path | Target | Stack | Deploy Method |
|------|--------|-------|---------------|
| A | WP Engine (existing clients) | Sage only | GitPush + committed vendor/ |
| B | VPS (new projects) | Bedrock + Sage + Trellis | Ansible + Composer |

The component packages are identical in both paths. Only the consumer wiring differs.
