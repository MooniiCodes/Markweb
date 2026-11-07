:::screen top
:::page title="Documentation"

MarkWeb is an AuroraOS 3DS app that renders **Markdown pages** (from `http://`/`https://` URLs or local files). It is not a full browser: it does not run JavaScript/CSS engines. Instead, it renders a supported subset of Markdown/HTML plus **Aurora MarkWeb directives** (`:::` lines) for page metadata, styling, dual-screen layout, boxes, and links.

## 1. How MarkWeb opens pages

### Supported locations
- **Remote URLs:** `http://...` and `https://...` (fetched via AuroraOS network API; device must be on Wi-Fi).
- **Local files:** `sd:/path/to/file.md`, `/Aurora/...`, or paths relative to the app directory.
- **Aliases:**
  - `markweb:homepage` — MarkWeb homepage (primary)
  - `markweb:home` — same
  - `about:home` — same

### Boot/home behavior
On startup, MarkWeb always loads the homepage URL:
```text
https://raw.githubusercontent.com/MooniiCodes/Markweb/main/Homepage.md
```
If that fails or returns empty/tiny content, it tries:
```text
https://raw.githubusercontent.com/MooniiCodes/Markweb/refs/heads/main/Homepage.md
```
If both fail, MarkWeb shows a **built-in fallback page** (never a blank screen) and reports the error in the status bar.

### Reload behavior
`X` / **Reload** reloads the **current page URL/path again** (re-fetches remote content). If you are on the homepage and it previously fell back to built-in content, reload retries the remote homepage URLs.

### Relative links
Links without a scheme (`http(s)://`, `sd:/`, `/`, `markweb:`, `about:`) are resolved relative to the current page’s base (directory of current URL/path).

## 2. Controls (user-facing)

| Input | Normal mode | Cursor mode |
|---|---|---|
| Up/Down | Scroll page | Move cursor |
| Left/Right | Select link | Move cursor |
| L/R | Page scroll | Page scroll |
| A | Open selected link | Click/open link under cursor |
| B | Go to homepage | Exit cursor mode |
| X | Reload current page | Reload current page |
| Y | Address bar | Address bar |
| START | Toggle cursor mode | Toggle cursor mode off |
| SELECT | Quit app | Quit app |
| Bottom touch buttons | Home / Address / Cursor / Reload | Same |
| Bottom touch (content area) | Scroll | Roughly positions cursor |

Address input supports URL/file paths and `markweb:homepage`.

## 3. Page titles

MarkWeb shows the page title in the top bar. Title sources (priority depends on parse order; explicit page/title directives and `<title>` are strong hints, first heading is used as fallback when no explicit title seen):

```md
:::page title="My Page Name"
```
```md
:::title title="My Page Name"
```
```html
<title>My Page Name</title>
```
```md
# My Page Name
```

Use `:::page` at the top of the file for clarity.

## 4. Markdown support

MarkWeb renders a practical subset:

| Feature | Syntax | Notes |
|---|---|---|
| Headings | `#` to `######` | Rendered with heading fonts/colors; first heading may become title fallback. |
| Paragraph text | Plain lines | Rendered as text lines. |
| Blank line | Empty line | Adds spacing. |
| Unordered list | `- item`, `* item`, `+ item` | Rendered with `*` prefix. |
| Ordered list | `1. item` | Simple `n.` detection. |
| Quote | `> text` | Rendered with accent bar/quote color. |
| Horizontal rule | `---`, `***`, `___` | Drawn as a line. |
| Fenced code block | ``` | Toggles preformatted code rendering; code background/color applied. |
| Inline formatting | `**bold**`, `*italic*`, `_italic_`, `` `code` `` | Stripped/simplified in current renderer (text preserved; rich inline styling is limited). |
| Link | `[label](url)` | Clickable/selectable link; resolved relative to current page if needed. |
| Image | `![alt](src)` | Rendered as a label `[image] alt/src` (not full remote image compositing yet). |
| Escapes/entities | `&amp; &lt; &gt; &quot; &#39;` | Basic HTML entities decoded in HTML-stripped text. |

### Links
Markdown:
```md
[Open MarkWeb repo](https://github.com/MooniiCodes/Markweb)
[Homepage alias](markweb:homepage)
[Local file](sd:/Aurora/Apps/C/MarkWeb/pages/example.md)
```
HTML:
```html
<a href="https://example.com">Example</a>
```

## 5. Basic HTML support

MarkWeb is **not** a full HTML/CSS/JS engine. Supported/basic handling:
- `<a href="...">text</a>` — extracts links.
- `<hr>` / `<hr />` — horizontal rule.
- `<br>` — treated as markup; effect may be limited in line renderer.
- `<pre> ... </pre>` — preformatted text mode.
- `<title>...</title>` — page title.
- `<h1>` — can set title fallback.
- Unknown tags are generally stripped from displayed text (`unhtml` behavior).
- No script execution, no forms, no CSS layout, no modern DOM.

## 6. Aurora MarkWeb directives (`:::`)

Directives are line-based commands starting with `:::`. Place them on their own lines. Unknown directives are ignored.

### `:::page` / `:::title`
Sets page metadata/title.
```md
:::page name="home" title="MarkWeb Home"
:::title title="MarkWeb Home"
```
Attributes parsed (simple quoted values): `title`, `name`.

### `:::style`
Customizes page colors. Values are hex colors (`#RRGGBB` or `RRGGBB`).
```md
:::style bg=#0f1318 fg=#e8eef6 link=#64e8c8 accent=#64e8c8 codebg=#171d24 h1=#64e8c8 h2=#8fd3ff quote=#9aa4b2 border=#2b3642 bar=#161b22
```

| Key | Affects |
|---|---|
| `bg` | Page background |
| `fg` | Default text |
| `link` | Link text / link UI accents |
| `accent` | UI accents, selected outlines, title accents |
| `codebg` | Code block / box background |
| `h1` | H1 color |
| `h2` | H2-H6 default color |
| `quote` | Quote text / status accents |
| `border` | Rules, box borders |
| `bar` | Top/bottom bars |

### `:::screen`
Routes subsequent content to a screen target (rendering/layout hint):
```md
:::screen top
# Top screen content

:::screen bottom
Bottom screen help or content

:::screen both
Content for both targets
```
Values: `top`, `bottom`, `both`.

### `:::box` / `:::endbox`
Draws a simple card/box container.
```md
:::box title="Quick Start"
- Step one
- Step two
:::endbox
```
`title` is parsed from quoted attribute (`title="..."`). Nested/complex box layout is limited by the line renderer.

### `:::link`
Creates a clickable link line.
```md
:::link Label text|https://example.com
:::link Homepage|markweb:homepage
:::link Local page|sd:/Aurora/Apps/C/MarkWeb/example.md
```
Format: `:::link <label>|<url>` (split at first `|`).

### `:::img`
Image directive (current renderer shows an image label/placeholder):
```md
:::img sd:/Aurora/Apps/C/MarkWeb/logo.png 200 80
:::img https://example.com/image.png
```

### `:::center`
Accepted as a decorative marker (limited/no-op depending on renderer state).

## 7. Full page template

```md
:::page name="docs" title="My MarkWeb Page"
:::style bg=#101418 fg=#e8eef6 link=#64e8c8 accent=#64e8c8 codebg=#171b22 h1=#64e8c8 h2=#8fd3ff quote=#9aa4b2 border=#2b3642 bar=#161b22

:::screen both
# My MarkWeb Page
Welcome to a custom MarkWeb document.

:::box title="Navigation"
:::link Home|markweb:homepage
:::link Project|https://github.com/MooniiCodes/Markweb
:::endbox

## Content
Normal Markdown works here.

- List item
- [Inline link](https://example.com)

> Quote block

```c
// fenced code
printf("Hello MarkWeb\n");
```

:::screen bottom
Use **B** for Home, **X** to reload, **START** for cursor mode.

---
*End of page*
```

## 8. Publishing/homepage notes

The app’s default homepage target is:
```text
https://raw.githubusercontent.com/MooniiCodes/Markweb/main/Homepage.md
```
It also tries:
```text
https://raw.githubusercontent.com/MooniiCodes/Markweb/refs/heads/main/Homepage.md
```

If the raw file is missing/empty, users see the built-in fallback page until the GitHub file contains valid markdown. Publish a real `Homepage.md` to make the remote homepage visible.

## 9. Limits / implementation notes

- Renderer is **line-based** and optimized for 3DS constraints (max source/lines/links are bounded).
- Not a web standards engine: complex HTML/CSS/JS, tables, advanced inline nesting, and remote image compositing are limited.
- `http_get`/TLS behavior depends on AuroraOS networking/TLS support on the device/build.
- Reload refetches the page; it does not “refresh view state” only.
- Cursor mode is for precise link clicking on the top screen; normal mode still supports D-Pad link selection.

## 10. Install

Copy:
- `new version\sdk\examples\markweb\markweb.bin` -> `SD:/Aurora/Apps/C/markweb.bin`

Optional app data path:
- `SD:/Aurora/Apps/C/MarkWeb/config.txt`

:::screen bottom

$[Back](Homepage.md)

# MarkWeb Documentation

## This documentation is written by a AI model.

If anything is wrong about the documentation, make a issue https://github.com/MooniiCodes/Markweb.

