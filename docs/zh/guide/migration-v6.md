# 从 v5 升级到 v6

`@wangeditor-next/editor@6.0.0` 的主要不兼容变化是内置视频节点的对齐和 HTML
输出结构。表格拖拽等同版本包含的其他修改属于缺陷修复。

## 升级依赖

```shell
npm install @wangeditor-next/editor@^6.0.0
```

React 项目需要同步升级适配器：

```shell
npm install @wangeditor-next/editor-for-react@^3.0.0
```

Vue 2、Vue 3 和可选插件请升级到各自的最新版本，并确保 lockfile 中实际解析到的
`@wangeditor-next/editor` 是 6.x。

## 视频节点对齐

v5 视频节点使用 `textAlign`。v6 改为媒体专用的 `align` 字段：

```ts
type VideoAlign = 'left' | 'center' | 'right'

const video = {
  type: 'video',
  src: 'https://example.com/video.mp4',
  align: 'center' as VideoAlign,
  children: [{ text: '' }],
}
```

旧的 `textAlign` 节点和 HTML 仍可导入，编辑器会按新的媒体对齐模型解释
`left`、`center` 和 `right`。`justify` 不再是视频的有效对齐值，无法识别的值会回退到居中。

## HTML 输出结构

v5 使用 `<div data-w-e-type="video">` 和 `text-align`。v6 使用全宽 flex 容器：

```html
<figure
  data-w-e-type="video"
  data-w-e-is-void
  data-w-e-align="center"
  style="display: flex; justify-content: center; margin: 0; max-width: 100%; width: 100%;"
>
  <video><!-- ... --></video>
</figure>
```

如果业务 CSS、HTML 清洗规则、服务端模板或自动化测试依赖旧的 `div` 标签、
`data-w-e-text-align` 或 `text-align`，升级时需要改为匹配 `figure[data-w-e-type="video"]`、
`data-w-e-align` 和 flex 对齐结构。

## 升级检查

1. 搜索自定义视频节点中的 `textAlign`，改为 `align`。
2. 搜索针对 `div[data-w-e-type="video"]` 的 CSS 和 DOM 查询。
3. 验证 `editor.getHtml()` 的视频输出能通过业务 HTML 清洗和存储链路。
4. 用历史内容执行一次 `editor.setHtml(oldHtml)`，确认视频位置和尺寸符合预期。
5. React 项目确认 `@wangeditor-next/editor-for-react` 已升级到 3.x。

完整发布内容见 [v6.0.0 release notes](https://github.com/wangeditor-next/wangEditor-next/releases/tag/v6.0.0)。
