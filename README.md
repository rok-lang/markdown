# markdown

A Rok library that converts Markdown to HTML. It covers the main parts of
CommonMark plus GitHub extensions (tables, strikethrough, callouts, footnotes,
heading anchors), and renders the pages of docs.rok-lang.org.

## Add it

```
rok add markdown
```

## Usage

```rok
use markdown::render;

let doc = render("# Title\n\nBody *emphasis*\n");
println(doc.html);     // <h1 id="title">..Title</h1> ..
println(doc.title);    // Title
```

Use `render_untrusted` for Markdown of unknown origin, and pass `Options` to map
link destinations or highlight code blocks. There is no highlighter dependency;
pass one through `Options.code` (pairs well with
[rok-syntax](https://github.com/rok-lang/rok-syntax)).

## Docs

https://docs.rok-lang.org

## License

Licensed under either of [MIT](LICENSE-MIT) or [Apache-2.0](LICENSE-APACHE), at
your option.
