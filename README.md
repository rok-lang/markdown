# rok-markdown

마크다운을 HTML 로 바꾸는 rok 라이브러리입니다. CommonMark 의 주요 부분과 GitHub 확장을 다룹니다. [rok-lang.org](https://rok-lang.org) 의 문서 페이지와 [rokstones.io](https://rokstones.io) 의 README 렌더링에 씁니다.

- 블록: ATX 헤딩 (GitHub 호환 `id` 와 `#` 앵커), 문단, fenced 코드 (```` ``` ```` / `~~~`), 인용, 중첩 목록 (tight / loose, 시작 번호), 구분선, `<div>` / `<section>` HTML 블록
- GFM: 표 (정렬, `\|`), 취소선 (`~~`), 콜아웃 (`> [!NOTE]` ..), 각주 (`[^1]` 와 `[^1]: ..`)
- 인라인: 코드, 강조 (CommonMark flanking 규칙), 링크, 이미지, 자동 링크 (`<https://..>`), 엔티티, 백슬래시 이스케이프, 강제 줄바꿈

## 사용

```rokon
Package {
    deps: { "rok-markdown": "0.1" },
}
```

```rok
use rok_markdown::{Options, render, render_untrusted};

let doc = render("# 제목\n\n본문 *강조*\n");
println(doc.html);       // <h1 id="제목"><a class="anchor" href="#제목" aria-hidden="true">#</a>제목</h1> ..
println(doc.title);      // 제목

// `.md` 링크를 `.html` 로, ```rok 블록은 rok-syntax 로 하이라이트
let opts = Options {
    link: |h| h.replace(".md", ".html"),
    code: |lang, code| if lang == "rok" { rok_syntax::highlight_rok(code) } else { none },
};
let page = render(src, opts);

// 어디서 왔는지 모르는 README
let readme = render_untrusted(text);
```

## API

| 항목 | 설명 |
|------|------|
| `render(src: str, opts: Options \| none = none) -> Doc` | 믿을 수 있는 마크다운. `<div>` / `<section>` 블록을 그대로 내보냄 |
| `render_untrusted(src: str, opts: Options \| none = none) -> Doc` | 아무 데서나 온 마크다운. HTML 블록은 글자로, 링크 / 이미지 주소의 scheme 이 `http` / `https` / `mailto` 가 아니면 `#` |
| `Options.link: fn(str) -> str`         | (모든 필드에 기본값이 있음) 적힌 링크 / 이미지 주소를 내보낼 `href` / `src` 로. 기본은 그대로 |
| `Options.code: fn(str, str) -> (str \| none)` | info 문자열의 첫 낱말과 코드로 `<pre><code>` 안의 HTML 을. `none` 이면 이스케이프만. 기본은 늘 `none` |
| `Options.callout: fn(str) -> str`      | 콜아웃 종류 (소문자) 의 제목. 기본은 `Note` / `Tip` / `Important` / `Warning` / `Caution` |
| `Doc { html, title, headings }`        | 결과. `title` 은 첫 1 단계 헤딩의 글자 |
| `Heading { level, id, text }`          | 문서의 헤딩 (목차용) |
| `esc(s: str) -> str`                   | `&` `<` `>` `"` 이스케이프 |
| `strip_tags(html: str) -> str`         | 태그를 빼고 기본 엔티티를 푼 글자 |
| `slug(text: str) -> str`               | GitHub 의 헤딩 앵커 |

인라인 HTML 은 어느 쪽이든 글자로 이스케이프합니다. 콜아웃 제목과 모든 속성 값은 이스케이프합니다. 하이라이터는 의존하지 않으며 `Options.code` 로 받습니다 ([rok-syntax](https://github.com/rok-lang/rok-syntax) 와 함께 쓰면 됩니다).

## 개발

```bash
rok test        # 테스트
rok fmt --check
rok doc         # target/doc/ 에 API 문서
```

## publish

아직 rokstones.io 에 publish 하지 않았습니다. 지금은 rok-lang.org 와 rokstones.io 가 path 의존 (`"rok-markdown": Dep::path("../stones/rok-markdown")`) 으로 씁니다. publish 순서는 다음과 같습니다.

1. 이 저장소를 `https://github.com/rok-lang/rok-markdown` 에 push 합니다.
2. `rok publish` 로 인덱스 줄을 미리 보고, [rokstones.io-index](https://github.com/rok-lang/rokstones.io-index) checkout 에서 `rok publish --index <checkout> --commit` 으로 커밋한 뒤 pull request 로 보냅니다.
3. 병합되면 두 사이트의 의존을 `"rok-markdown": "0.1"` 로 바꿉니다.

매니페스트에 아직 `description` / `license` / `repository` 필드가 없어서 `rok.rokon` 머리 주석에 적어 두었습니다. 필드가 생기면 옮깁니다.

## 라이선스

[MIT](LICENSE-MIT) 또는 [Apache-2.0](LICENSE-APACHE) 중 하나를 골라 쓸 수 있습니다.
