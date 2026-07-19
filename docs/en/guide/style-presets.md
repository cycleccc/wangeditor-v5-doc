# Custom Style Presets

`@wangeditor-next/plugin-style-presets` adds semantic text and block style presets to wangEditor v6. Stored content keeps a stable preset key while application CSS controls presentation, so themes can change without rewriting historical content.

The plugin is opt-in. It does not change the existing toolbar, node structure, or HTML output unless it is registered and configured.

See the [live demo](https://wangeditor-next.github.io/demo/custom-styles.html?lang=en).

## Install and Register

```shell
pnpm add @wangeditor-next/plugin-style-presets
```

```ts
import { Boot } from '@wangeditor-next/editor'
import stylePresetsModule from '@wangeditor-next/plugin-style-presets'

// Register once before creating any editor
Boot.registerModule(stylePresetsModule)
```

## Configure the Toolbar

```ts
const editorConfig = {
  MENU_CONF: {
    stylePreset: {
      presets: [
        {
          key: 'muted-text',
          title: 'Muted text',
          scope: 'text',
          className: 'article-muted',
        },
        {
          key: 'lead-paragraph',
          title: 'Lead paragraph',
          scope: 'block',
          className: 'article-lead',
        },
      ],
    },
  },
}

const toolbarConfig = {
  insertKeys: {
    index: 4,
    keys: ['stylePreset'],
  },
}
```

- `key`: Stable kebab-case identifier stored in JSON and HTML. Do not rename it when changing themes.
- `title`: Label displayed in the toolbar menu.
- `scope: 'text'`: Applies to selected text or subsequent input.
- `scope: 'block'`: Applies to selected paragraphs, headings, list items, and other blocks.
- `className`: Optional application class names separated by spaces.

`key`, `title`, and `scope` are required. The plugin throws while reading configuration for duplicate or invalid keys, invalid classes, or overlapping class mappings in the same scope.

## Add Application CSS

```css
.article-muted {
  color: var(--article-muted-color, #667085);
  font-size: 0.875rem;
}

.article-lead {
  color: var(--article-lead-color, #344054);
  font-size: 1.125rem;
  line-height: 1.75;
}
```

Load the same application CSS in the editor and in standalone HTML viewers. The plugin always emits `w-e-style-preset-${key}`, so you can also omit `className` and target the generated class directly:

```css
.w-e-style-preset-lead-paragraph {
  font-size: 1.125rem;
  line-height: 1.75;
}
```

## Data and HTML

A text preset is stored on text nodes:

```json
{ "text": "Muted content", "stylePreset": "muted-text" }
```

```html
<span class="w-e-style-preset-muted-text article-muted" data-w-e-style-preset="muted-text">Muted content</span>
```

A block preset is stored on a paragraph, heading, or another block node:

```html
<p class="w-e-style-preset-lead-paragraph article-lead" data-w-e-style-preset="lead-paragraph">
  Article lead
</p>
```

HTML import prefers `data-w-e-style-preset`. If that attribute is absent, the plugin can restore a preset from its configured application class. Unknown data keys remain in the document, so adding the configuration later restores their application styling.

## Command API

```ts
import {
  applyStylePreset,
  getActiveStylePreset,
  removeStylePreset,
} from '@wangeditor-next/plugin-style-presets'

applyStylePreset(editor, 'muted-text')
getActiveStylePreset(editor) // 'muted-text' | null
removeStylePreset(editor, 'text') // or 'block' / 'all'
```

## Relationship to Existing Style Modes

- The plugin supports `@wangeditor-next/editor >= 6.0.0`.
- Existing `inline` and `class` modes still handle low-level formats such as color, font size, and line height. The plugin does not replace them.
- Use presets for application semantics such as leads and callouts. Avoid embedding concrete colors or sizes in a preset `key`.
- The plugin does not enter the default toolbar or migrate existing JSON and HTML automatically.
- Output supports `Slate -> HTML -> Slate`, `HTML -> Slate -> HTML`, and `setHtml(getHtml())` round trips.

For strict CSP environments, also see [CSP Class Style Mode](./csp-class-mode.md).
