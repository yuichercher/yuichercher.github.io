# Design

The visual and typographic spec for 勺子的深夜食堂. Implementation follows this document. To change a value here, update the document first, then the code.

## Principles

A single centered column, quiet and restrained. Reading a post should feel like reading a sheet of paper. Reference: Dan Abramov's overreacted.io.

No top navigation bar, sidebar table of contents, banners, tag clouds, or decorative icons.

## Home page

- Top left: spoon icon + site title. The largest text on the site, rose pink, weight 800.
- To the right of the title, in order: avatar (opens a profile card), language switch, theme switch.
- Below the title: a one-line introduction.
- Post list: each entry shows the title (rose pink, weight 800), date · reading time, and a one-line summary.
- No separate About page. Profile information lives in the avatar card.

## Post page

- Title in the body text color, not pink, weight 800. On the home page titles are links, so they are pink. Inside a post, pink is reserved for links.
- Below the title: date · reading time.
- No table of contents.
- At the end: `← Home`, `Previous`, `Next`.

## Color

Light mode uses a single accent, rose pink. Dark mode pairs pink with periwinkle blue.

Light mode avoids blue because rose pink and periwinkle are both mid-lightness, high-saturation colors on white. At equal "volume" they compete, and the page looks busy. In dark mode both become light, low-saturation tints and sit together comfortably on a dark background.

Colors are only used through CSS custom properties. No raw color values in components.

| Token | Light | Dark | Used for |
|---|---|---|---|
| `--bg` | `#ffffff` | `#282c35` | Page background |
| `--text` | `#2a2a2e` | `#e6e6ea` | Body text, post titles |
| `--text-muted` | `#6b6b70` | `#9a9aa3` | Dates, summaries, secondary text |
| `--primary` | `#c23a6f` | `#ff9ec4` | Site title, home page post titles, links |
| `--line` | `#ececef` | `#3a3f4a` | Dividers |
| `--quote-line` | `#f0c3d4` | `#a9b4ff` | Blockquote left border |
| `--selection` | `#fce8ef` | `#2a2d42` | Text selection background |
| `--code-bg` | `#ffffff` | `#2a2d42` | Code block background |
| `--code-border` | `#ececef` | `transparent` | Code block border |
| `--code-keyword` | `#c23a6f` | `#a9b4ff` | Code keywords |
| `--code-number` | `#c23a6f` | `#a9b4ff` | Code numbers |
| `--code-function` | `#2a2a2e` | `#ff9ec4` | Code function names |
| `--code-comment` | `#9a9aa3` | `#7f8496` | Code comments |

- Light mode code blocks: white background, 1px light gray border, 8px radius. No gray fill: mid-lightness, low-saturation colors read as "dirty".
- Links: underlined, about 3px underline offset, 1px thickness.
- Text color against its background meets WCAG AA (4.5:1). Rose pink on white is about 5.1:1.

## Type

```css
font-family: 'Geist', 'PingFang SC', 'MiSans', sans-serif;
```

- Latin: Geist.
- Chinese: Apple devices use the system PingFang SC (its license doesn't allow distributing it as a web font, but Apple devices already have it). Other devices load MiSans.
- The Latin font comes first, so letters and numbers render in Geist and Chinese characters fall through to the Chinese fonts.
- No italics anywhere. Geist has no true italic, and Chinese has no italic, so the browser would only slant the glyphs. Use bold or color for emphasis.
- Put a space between Chinese and Latin text.

## Typography (starting values, open to tuning)

- Body: about 17px, line height 1.75–1.8.
- Measure: about 65 characters for English, 30–40 characters for Chinese.
- Headings: weight 800, letter spacing slightly tightened (about -0.01em).

## Theme switching

- Three options: light, dark, system. Default: system.
- Remember the user's choice.
- No flash of the wrong theme on load.
