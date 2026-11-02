# AGENTS.md

Guidelines for coding agents working in this repository. Project knowledge (design spec, technical decisions, conventions) lives in `docs/`. This file only covers how to collaborate.

## Read before you start

- [`docs/design.md`](docs/design.md): design spec (layout, color, type, spacing)
- [`docs/conventions.md`](docs/conventions.md): engineering and commit conventions
- [`docs/decisions/`](docs/decisions/): technical decisions made so far

## How we work

### Roles

You write the code. The author makes the decisions and reviews every change.

### Every change

- Do one thing at a time. Keep changes small enough to review line by line.
- After each change, explain what you changed, why you wrote it this way, which alternatives you considered, and why you didn't choose them.
- Don't add features that weren't asked for. Don't "improve" unrelated code along the way.
- Prefer plain, readable code. Add a short comment where something isn't obvious.

### Record decisions

When you make a technical choice, add a record to `docs/decisions/` with the next number. Follow the structure of `0001`: context, decision, alternatives, consequences. Explain the underlying reasoning so a reader could explain it to someone else, not just a list of conclusions.

Every record is bilingual:

- English: `NNNN-title.md`, e.g. `0003-theme-switching.md`
- Chinese: `NNNN-title.zh.md`, e.g. `0003-theme-switching.zh.md`
- Both versions carry the same content, with a language link at the top of each pointing to the other.
- Add the new record to the table in `docs/decisions/README.md`.

These areas must have a record when implemented: theme switching (including how to avoid a flash of the wrong theme on load), font loading, design token organization, embedding and lazy-loading interactive components, and internationalization.

### Dependencies

Ask before adding any dependency. Say what problem it solves, whether it can be done without one, and roughly how large it is. Fewer dependencies is better.

### When unsure

- Don't change values defined in `docs/design.md` (colors, fonts, etc.) on your own. If something looks wrong, raise it.
- Open questions are tracked in the repository's GitHub Issues. If you run into one, ask the author instead of deciding.

### Follow the official docs

Astro moves fast and many tutorials online are outdated. Check the installed version in `package.json` and follow the official docs for that version. For example, since Astro 6, `z` is imported from `astro/zod`, not `astro:content`.
