# Sage vs EtchWP

We evaluated two paths for the new stack.

## Sage 10 — Selected

Sage is an open-source WordPress theme framework by Roots (MIT licensed, 10+ years development, large community). It uses Blade templates, Tailwind CSS, and a modern build pipeline (Bud).

**Why it fits:**

- **Native Tailwind CSS.** No framework lock-in, no proprietary CSS system.
- **Components are real files** (PHP, Blade, JS). Version controlled, diffable, distributable via Composer packages.
- **Zero vendor lock-in.** MIT licensed. If Roots disappeared tomorrow, every site keeps working.
- **Skills transfer** beyond WordPress. Blade and Tailwind are standard web dev skills the team can use anywhere.
- **We already have a live Sage build** (accu-shot.com) to extract from.

## EtchWP — Not Selected

Etch (by Digital Gravy / Kevin Geary) is a visual builder that outputs clean code and syncs to Gutenberg blocks. Genuinely impressive tool.

**Why it doesn't fit:**

- Uses **Automatic.css (ACSS)**, not Tailwind. Fundamentally different CSS methodology from what we're committing to.
- Components are **JSON blobs in the WordPress database**, not version-controlled files. Cannot wire to Composer. Cannot be diffed, reviewed, or packaged.
- **Proprietary and commercial.** Agency and every client site locked to Digital Gravy's licensing, pricing, and product roadmap.
- **No migration path out.** Components built in Etch are Etch-specific JSON. Leaving means rewriting, not porting.
- Still **v1.5.x** with an explicitly experimental API surface.

Etch is the right tool for a different kind of agency. It's the wrong tool for a Composer-distributed, Tailwind-based component library.
