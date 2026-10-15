# markdown

A rok library that converts Markdown to HTML. It covers the main parts of CommonMark plus GitHub extensions, and renders the documentation pages of [rok-lang.org](https://rok-lang.org) and the READMEs on [rokstones.io](https://rokstones.io).

- Blocks: ATX headings (GitHub-compatible `id` and `#` anchor), paragraphs, fenced code (```` ``` ```` / `~~~`), block quotes, nested lists (tight / loose, start number), thematic breaks, `<div>` / `<section>` HTML blocks
- GFM: tables (alignment, `\|`), strikethrough (`~~`), callouts (`> [!NOTE]` ..), footnotes (`[^1]` and `[^1]: ..`)
- Inline: code, emphasis (CommonMark flanking rules), links, images, autolinks (`<https://..>`), entities, backslash escapes, hard line breaks

## Usage

```rokon
Package {
    deps: { "markdown": "0.1" },
}
```

```rok
use markdown::{Options, render, render_untrusted};

let doc = render("# Title\n\nBody *emphasis*\n");
println(doc.html);       // <h1 id="title"><a class="anchor" href="#title" aria-hidden="true">#</a>Title</h1> ..
println(doc.title);      // Title

// `.md` links to `.html`, ```rok blocks highlighted with rok-syntax
let opts = Options {
    link: |h| h.replace(".md", ".html"),
    code: |lang, code| if lang == "rok" { rok_syntax::highlight_rok(code) } else { none },
};
let page = render(src, opts);

// a README of unknown origin
let readme = render_untrusted(text);
```

## API

| Item | Description |
|------|-------------|
| `render(src: str, opts: Options \| none = none) -> Doc` | Trusted Markdown. Emits `<div>` / `<section>` blocks as is |
| `render_untrusted(src: str, opts: Options \| none = none) -> Doc` | Markdown from anywhere. HTML blocks become text; link / image URLs whose scheme is not `http` / `https` / `mailto` become `#` |
| `Options.link: fn(str) -> str`         | (Every field has a default.) Maps a link / image destination as written to the emitted `href` / `src`. Default: unchanged |
| `Options.code: fn(str, str) -> (str \| none)` | HTML inside `<pre><code>` from the info string's first word and the code. `none` only escapes. Default: always `none` |
| `Options.callout: fn(str) -> str`      | Title of a callout from its kind (lowercase). Default: `Note` / `Tip` / `Important` / `Warning` / `Caution` |
| `Doc { html, title, headings }`        | The result. `title` is the text of the first level-1 heading |
| `Heading { level, id, text }`          | A heading of the document (for tables of contents) |
| `esc(s: str) -> str`                   | Escapes `&` `<` `>` `"` |
| `strip_tags(html: str) -> str`         | Text with tags removed and basic entities decoded |
| `slug(text: str) -> str`               | GitHub's heading anchor |

Inline HTML is escaped as text in both modes. Callout titles and all attribute values are escaped. There is no highlighter dependency; pass one through `Options.code` (pairs well with [rok-syntax](https://github.com/rok-lang/rok-syntax)).

## Development

```bash
rok test        # tests
rok fmt --check
rok doc         # API docs in target/doc/
```

## Publishing

Not yet published to rokstones.io. For now rok-lang.org and rokstones.io use it as a path dependency (`"markdown": Dep::path("../stones/markdown")`). To publish:

1. Push this repository to `https://github.com/rok-lang/markdown`.
2. Preview the index line with `rok publish`, commit it with `rok publish --index <checkout> --commit` in a [rokstones.io-index](https://github.com/rok-lang/rokstones.io-index) checkout, and send a pull request.
3. Once merged, change both sites' dependency to `"markdown": "0.1"`.

## License

Licensed under either of [MIT](LICENSE-MIT) or [Apache-2.0](LICENSE-APACHE), at your option.
