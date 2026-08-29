# 自定义样式预设

`@wangeditor-next/plugin-style-presets` 为 wangEditor v6 提供语义化的文本和块级样式预设。内容保存稳定的 preset key，实际视觉效果由业务 CSS 控制，因此修改主题时无需重写历史内容。

插件默认不注册，也不会改变现有工具栏、节点结构或 HTML 输出。现有项目可按需启用。

查看[在线示例](https://wangeditor-next.github.io/demo/custom-styles.html)。

## 安装和注册

```shell
pnpm add @wangeditor-next/plugin-style-presets
```

```ts
import { Boot } from '@wangeditor-next/editor'
import stylePresetsModule from '@wangeditor-next/plugin-style-presets'

// 在创建编辑器之前注册，整个应用只注册一次
Boot.registerModule(stylePresetsModule)
```

## 配置工具栏

```ts
const editorConfig = {
  MENU_CONF: {
    stylePreset: {
      presets: [
        {
          key: 'muted-text',
          title: '辅助文字',
          scope: 'text',
          className: 'article-muted',
        },
        {
          key: 'lead-paragraph',
          title: '导语',
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

- `key`：写入 JSON 和 HTML 的稳定 kebab-case 标识。发布后不要因为主题调整而修改。
- `title`：工具栏菜单中的显示名称。
- `scope: 'text'`：应用到所选文字或后续输入的文字。
- `scope: 'block'`：应用到选区中的段落、标题、列表项等块节点。
- `className`：可选的业务 class，可用空格配置多个 class。

`key`、`title` 和 `scope` 必填。重复 key、无效 key、无效 class 或同一 scope 下存在包含关系的歧义 class 组合会在读取配置时抛出错误。

## 编写业务 CSS

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

编辑器区域和独立 HTML 展示区域都需要加载相同的业务 CSS。插件还会固定输出 `w-e-style-preset-${key}`，因此也可以不配置 `className`，直接使用生成的 class：

```css
.w-e-style-preset-lead-paragraph {
  font-size: 1.125rem;
  line-height: 1.75;
}
```

## 数据和 HTML

文本预设会保存到文本节点：

```json
{ "text": "提示内容", "stylePreset": "muted-text" }
```

```html
<span class="w-e-style-preset-muted-text article-muted" data-w-e-style-preset="muted-text">提示内容</span>
```

块级预设会保存到段落、标题或其他块节点：

```html
<p class="w-e-style-preset-lead-paragraph article-lead" data-w-e-style-preset="lead-paragraph">
  文章导语
</p>
```

导入 HTML 时优先读取 `data-w-e-style-preset`。没有 data 属性时，插件也能根据当前配置的业务 class 恢复 preset。尚未配置的 data key 会继续保留在内容中，补回配置后即可恢复对应的业务样式。

## 命令 API

```ts
import {
  applyStylePreset,
  getActiveStylePreset,
  removeStylePreset,
} from '@wangeditor-next/plugin-style-presets'

applyStylePreset(editor, 'muted-text')
getActiveStylePreset(editor) // 'muted-text' | null
removeStylePreset(editor, 'text') // 也可传入 'block' 或 'all'
```

## 与现有样式模式的关系

- 插件支持 `@wangeditor-next/editor >= 6.0.0`。
- 现有 `inline` / `class` 样式模式继续负责颜色、字号、行高等底层格式，两者无需替换。
- preset 适合保存“导语”“提示块”等业务语义，不建议把具体颜色或字号直接写入 `key`。
- 插件不会自动加入默认工具栏，也不会自动迁移历史 JSON 或 HTML。
- 输出支持 `Slate -> HTML -> Slate`、`HTML -> Slate -> HTML` 和 `setHtml(getHtml())` 回环。

对于严格 CSP 场景，可同时参考 [CSP class 样式模式](./csp-class-mode.md)。
