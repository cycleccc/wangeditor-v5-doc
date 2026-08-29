# Content

## Get Content

### Get HTML and Text

Use `editor.getHtml()` to get HTML content, see [demo](https://wangeditor-next.github.io/demo/get-html.html?lang=en). Use `editor.getText()` to get text content.

If you need HTML with unique id attributes (for node tracking/positioning), use `editor.getHtmlWithId(idKey?)`:

```js
const html = editor.getHtmlWithId() // default data-w-e-id
const html2 = editor.getHtmlWithId('data-node-id') // custom attribute key
```

PS: HTML format is recommended.

### Get JSON

Use `editor.children` to get JSON content.

You can convert JSON to HTML or text format in browser and nodejs.<br>
If in nodejs, you should exec `yarn add jsdom global-jsdom` firstly, then `require('global-jsdom/register')` in front of the below codes.

```js
const editor = createEditor({ content }) // `content` is JSON content
const html = editor.getHtml()
const text = editor.getText()
```

### Custom Style

Depending on the content and configuration, `editor.getHtml()` can emit elements, classes, `data-w-e-*` attributes, and inline styles for some built-in formats in the default mode. Load both the editor's base stylesheet and your application CSS when rendering the HTML.

Use [Custom Style Presets](./style-presets.md) when application concepts such as leads, callouts, or muted text should remain stable in stored content. The plugin persists a preset key in JSON and HTML while application CSS controls its appearance.

See these demos:

- [Get and render HTML](https://wangeditor-next.github.io/demo/get-html.html?lang=en)
- [Custom CSS style](https://wangeditor-next.github.io/demo/css/view.css)
- [Custom style presets](https://wangeditor-next.github.io/demo/custom-styles.html?lang=en)

You should use [Prism.js](https://prismjs.com/) to highlight code block by yourself. See [demo](https://wangeditor-next.github.io/demo/code-highlight.html?lang=en).
### Editor Content Styles

To keep editing predictable when the host page includes `reset.css`, Tailwind CSS Preflight, or similar global styles, wangEditor provides scoped base content styles inside the editable area. The scope root is `.w-e-text-container [data-slate-editor]`; it preserves structural behavior such as heading hierarchy, paragraph and list spacing, list markers, code blocks, and table borders.

This is CSS scoping, not iframe or Shadow DOM isolation. You can customize fonts, colors, and spacing with selectors scoped to the editor container or `[data-slate-editor]`. Avoid unscoped `!important` rules that override structural editor styles.

This behavior applies only inside the editor. `editor.getHtml()` still returns pure HTML without these styles. When rendering that HTML in an article, comment, or any page outside the editor, your application must provide the corresponding content CSS.

This convention addresses [Issue #988](https://github.com/wangeditor-next/wangEditor-next/issues/988) and is implemented in [PR #992](https://github.com/wangeditor-next/wangEditor-next/pull/992).

## Set Content

You can set your custom content when creating an editor.

### Set HTML

<b style="color: red;">Be careful: wangEditor can only understand the HTML format from `editor.getHtml()`, but not all HTML formats.</b>

For instance, wangEditor can understand `<strong>hello</strong>`, but can not understand `<span style="font-weight:bold;"></span>`.

### Set HTML when create editor

```js
const editor = createEditor({
  html: '<p>hello <strong>world</strong></p>', // html content, got from `editor.getHtml()`
  // other props ...
})
```

### Set HTML after create editor

See [demo](https://wangeditor-next.github.io/demo/set-html.html?lang=en)

```js
editor.setHtml('<p>hello <strong>world</strong></p>')
```

:::tip
`setHtml` is mainly used for re-write editor HTML content which got by `editor.getHtml()`.<br>
If you want to insert some HTML, use [dangerouslyInsertHtml](./API.md#dangerouslyinserthtml) please.
:::

### Set Text

```js
// 1. Convert text to HTML format
const text = '...' // text content
const html = text.split(/\n/).map(line => `<p>${line}</p>`).join('\n')

// 2. set HTML
const editor = createEditor({
  html,
  // other props ...
})

// 3. or setHtml after create editor
// editor.setHtml(html)
```

### Set JSON

```js
const editor = createEditor({
  content: [...], // JSON content, got from `editor.children`
  // other props ...
})
```

### Ajax async set content

You can create editor after ajax success callback.

```js
// pseudo code
import { IDomEditor } from '@wangeditor-next/editor'

let editor: IDomEditor | null = null  // TS syntax
// let editor = null                  // JS syntax

ajax(url, res => {
  editor = createEditor({
    // content or html
    // other props...
  })
})
```

::: tip
Goto [API](./API.md) to checkout more content APIs.
:::
