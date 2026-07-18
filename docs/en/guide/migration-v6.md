# Upgrade from v5 to v6

The main breaking change in `@wangeditor-next/editor@6.0.0` is the alignment model and exported
HTML structure of built-in video nodes. Other changes in the same release, including table drag
behavior, are bug fixes.

## Upgrade dependencies

```shell
npm install @wangeditor-next/editor@^6.0.0
```

React projects must upgrade the wrapper at the same time:

```shell
npm install @wangeditor-next/editor-for-react@^3.0.0
```

For Vue 2, Vue 3, and optional plugins, install their latest releases and verify that the lockfile
actually resolves `@wangeditor-next/editor` to 6.x.

## Video node alignment

v5 video nodes used `textAlign`. v6 uses the media-specific `align` field:

```ts
type VideoAlign = 'left' | 'center' | 'right'

const video = {
  type: 'video',
  src: 'https://example.com/video.mp4',
  align: 'center' as VideoAlign,
  children: [{ text: '' }],
}
```

Legacy `textAlign` nodes and HTML remain importable. The editor maps `left`, `center`, and `right`
to the new media alignment model. `justify` is no longer valid for videos, and unknown values fall
back to center alignment.

## Exported HTML structure

v5 used `<div data-w-e-type="video">` with `text-align`. v6 exports a full-width flex container:

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

If application CSS, HTML sanitizers, server-side templates, or automated tests depend on the old
`div` tag, `data-w-e-text-align`, or `text-align`, update them to support
`figure[data-w-e-type="video"]`, `data-w-e-align`, and the flex alignment structure.

## Upgrade checklist

1. Replace `textAlign` with `align` in custom video nodes.
2. Find CSS and DOM queries that target `div[data-w-e-type="video"]`.
3. Verify that video HTML from `editor.getHtml()` passes application sanitization and persistence.
4. Run `editor.setHtml(oldHtml)` with existing content and verify video position and size.
5. In React projects, verify that `@wangeditor-next/editor-for-react` is on 3.x.

See the [v6.0.0 release notes](https://github.com/wangeditor-next/wangEditor-next/releases/tag/v6.0.0)
for the complete release.
