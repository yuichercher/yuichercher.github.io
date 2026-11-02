# 0001 · Use Astro and MDX

English · [中文](0001-astro-and-mdx.zh.md)

- Status: accepted
- Date: 2026-10

## Context

This is a personal technical blog built for reading. Most pages are static posts, but some posts should embed interactive demos, such as a sliding-window frequency control demo with adjustable parameters.

Requirements: fast pages; writing posts should feel like writing Markdown; when needed, embed interactive components written in React.

## Decision

Use Astro as the framework and MDX for posts. Plain-text posts use regular Markdown (`.md`). Only posts with interactive components use MDX (`.mdx`).

## Why Astro

Astro is a meta-framework for content-driven sites, at the same layer as Next.js: it handles routing, how pages are rendered, and how the site is built and deployed. It builds on Vite.

Its core idea is shipping no JavaScript by default. Pages are rendered to plain HTML at build time, and only components explicitly marked as interactive load JavaScript. This is called the islands architecture, or partial hydration:

```astro
<FreqDemo client:visible />  <!-- loads this component's JS only when it scrolls into view -->
```

A typical post is mostly static text with one or two demos. With Astro, readers only download JavaScript for those demos. Everything else is plain HTML and loads fast.

Components can still be written in React, with no new component syntax to learn.

## Why MDX

MDX is Markdown plus JSX. A post can import a component and use it like an HTML tag:

```mdx
import FreqDemo from '../components/FreqDemo.jsx';

Adjust the time window and the limit in the demo below:

<FreqDemo window={60} limit={3} client:visible />
```

## Alternatives

### Next.js

The React team's recommended meta-framework. Built for full web applications and the most capable option.

Not chosen: pages ship the React runtime by default, which is heavy for a site that is mostly static posts, and it takes more setup. Next.js is the better fit for a future product with accounts and a database.

### VitePress

Minimal configuration, well suited to documentation sites.

Not chosen: its documentation-oriented design makes embedding interactive components less natural than MDX.

### Markdoc

Stripe's open-source authoring format. Posts can't contain JavaScript, only tags registered ahead of time in a config file, such as `{% callout %}`.

Its strengths are real:

- More forgiving syntax. MDX parses `<` and `{` in prose as JSX, so writing `O(n) < O(n²)` fails to compile unless escaped. Markdoc doesn't have this problem.
- Content is data, not code, so translation and collaboration are less likely to break it.

Not chosen:

- Its constraints are designed for large documentation sites with many authors. This blog has one author, who is a frontend engineer, so the constraints add little.
- The point of the blog is interactive demos. In Markdoc, every component needs a registered tag and attribute schema first. In Astro, Markdoc tags can't use directives like `client:visible` directly, so interactive components need an extra Astro wrapper. Every demo costs an extra step.
- MDX has a larger ecosystem and more learning resources.

MDX's syntax pitfalls are avoided by writing plain posts in `.md` and only interactive posts in `.mdx`.

## Consequences

- In `.mdx` posts, `<` and `{` in prose must be escaped or placed in code blocks.
- MDX doesn't support HTML comments (`<!-- -->`).
- Astro moves fast and many tutorials are outdated. Follow the official docs for the installed version.
- If every post later needs to be translated into multiple languages with batch translation tools, JSX in MDX posts is easy to break during translation. Revisit this decision then.
