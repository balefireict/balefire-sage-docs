# Path B: VPS + Trellis

Full Roots stack for dedicated server projects where WordPress is the whole box.

## The Stack

| Component | Purpose |
|-----------|---------|
| Bedrock | Composer-managed WordPress core, plugins, themes |
| Sage | The theme (Blade + Tailwind) |
| Trellis | Ansible-provisioned server (nginx, PHP-FPM, MariaDB, Redis) |

## How it works

1. Trellis provisions a bare Debian/Ubuntu server via Ansible playbook
2. Server gets: nginx, PHP-FPM, MariaDB, Redis, certbot
3. Bedrock manages WP core + plugins + themes via Composer at the root
4. Component packages are required in the root composer.json
5. Composer runs on deploy — no committed vendor/
6. Trellis deploy = Capistrano-style symlink swap (atomic, instant rollback)

## Pros

- **Atomic deploys.** New release goes live instantly, old release stays for rollback. Zero downtime.
- **Server provisioning as code.** Spin up a new server, run one command, get an identical stack.
- **Environment parity.** Dev, staging, production all provisioned from same config.
- **Secrets management.** Vault-encrypted group_vars for DB passwords, salts, API keys.
- **Composer on deploy.** No committed vendor/. Clean dependency resolution.

## When to use Path B

- Dedicated server projects where WordPress is the whole box
- When you need atomic deploys and instant rollback
- When the client needs environment parity (dev/staging/prod)

## When NOT to use Path B

- WPE-hosted sites (use Path A)
- Servers running multiple services (use Dokploy containerization instead)
- When the team hasn't learned Ansible yet (learning curve is real)
