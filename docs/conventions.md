# Conventions

## Structure and naming

- Design tokens live in `src/styles/tokens.css`.
- Posts live in `src/content/posts/`.
- Plain-text posts use `.md`. Only posts with interactive components use `.mdx`. MDX parses `<` and `{` in prose as code, so keeping plain posts in Markdown avoids escaping.
- Post URLs use English slugs, e.g. `/frequency-control`.
- File names use kebab-case.

## Code

- TypeScript in strict mode.
- Frontmatter is validated with a content collection schema, so a wrong field fails the build.
- Pin the Node version and commit the lockfile.
- Keep an `.editorconfig` and a clean `.gitignore`.

## Content and assets

- Remove template leftovers: sample posts, default favicon, default site title, unused dependencies.
- Compress images before committing. Don't commit multi-megabyte originals.
- API keys and analytics tokens go in environment variables, never in the repository.

## Commits

Use [Conventional Commits](https://www.conventionalcommits.org/):

```
type(optional scope): description
```

| Type | When to use |
|---|---|
| `feat` | New features, including new visual design |
| `fix` | Bug fixes |
| `style` | Code formatting only (indentation, semicolons), no logic change. Not CSS. |
| `refactor` | Restructuring without changing behavior |
| `docs` | Documentation, including the README and decision records |
| `chore` | Dependencies, configuration, cleanup |
| `ci` | Deployment and automation |
| `post` | Adding or editing posts (custom type) |

- One commit, one change.
- Describe what changed. Avoid messages like `update` or `fix bug`.
