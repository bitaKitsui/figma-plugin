---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
# use UnoCSS
css: unocss
---

# FigmaでPluginを作り
# サッカーをする

---

# 自己紹介

橘井 貴輝（PDU/FE）

- Figmaはちょっと触った程度
- FE勉強会やってます（毎週火曜 12:00 ~ 13:00） 
- 最近心霊ビデオにハマり中（闇動画など）
- 『霊障』という Zine 絶賛発売中
https://booth.pm/ja/items/3068546

---

# Agenda

- 概要説明
- Figma Plugin
- Smart Animate
- 実演
- まとめ

---

# 概要

- Figma上にモーダルを表示する
- ボタンを押すと Unsplash API 経由で画像を表示する
- Smart Animate 機能でサッカーをする

---
# Plugin作成

今回の使用技術

- Svelte
- TypeScript
- Rollup

---

# Plugin作成

まずはプラグインの初期化

Menu => Plugins => Development => New Plugin...

<img src="/plugin1.png" alt="" style="width: 600px; margin: 0 auto">

---

# Plugin作成

Figma design を選択して Next

<img src="/plugin2.png" alt="" style="width: 600px; margin: 0 auto">

---

# Plugin作成

Empty を選択、任意の名前を入力して Save as

<img src="/plugin3.png" alt="" style="width: 600px; margin: 0 auto">

---

# Plugin作成

適当な場所に名前を付けて保存することで、指定したディレクトリ配下にファイルが生成される

- `code.js`
- `manifest.json`

```json
{
  "name": "Tutorial",
  "id": "1234567890123456789",
  "api": "1.0.0",
  "main": "code.js",
  "editorType": ["figma"]
}
```

---

# Plugin作成

`manifest.json` とは、プラグインに関する設定が含まれるファイル

- `name`: メニューに表示されるプラグインの名前
- `id`: 新規プラグイン作成時に振り分けられる一意のID
- `api`: プラグインが使用するFigma APIのバージョン
- `main`: プラグインのJavaScriptコードへの相対パス
- `ui`: `figma.showUI` を介して iframe モーダルで使用できるHTMLファイル
- `editorType`: プラグインがどのエディタに表示されるか

---

# Plugin作成

`manifest.json` の `main` を修正、 `ui` を追加<br>
今回は Rollup でバンドルするため `dist` 配下を指定

```json {5,6}
{
  "name": "Tutorial",
  "id": "1234567890123456789",
  "api": "1.0.0",
  "main": "dist/code.js",
  "ui": "dist/ui.html",
  "editorType": ["figma"]
}
```

---

# Plugin作成

おまじない ~ `tsconfig.json` とか色々設定

今回は Svelte なので、 `figma-plugin-ds-svelte` というプラグイン

```shell 
npm init -y
npm install @figma/plugin-typings @rollup/plugin-commonjs @rollup/plugin-html @rollup/plugin-node-resolve @rollup/plugin-typescript cssnano figma-plugin-ds-svelte postcss rollup rollup-plugin-postcss rollup-plugin-svelte rollup-plugin-svg rollup-plugin-terser svelte tslib typescript
```

---

# Figmaの Plugin API

`@figma/plugin-typings` を入れることで、 `figma` グローバルオブジェクトを通じて API をいじることができる

ファイルの内容を表示、作成、更新するためのプロパティや関数が用意されている

- サムネイルやundoの履歴、バージョン履歴などの表示や更新
- レイヤーの作成、コピー、結合
- 既存のレイヤーのプロパティ調整
- ローカルスタイルの作成、使用、管理
- 外部ライブラリからスタイルとコンポーネントをインポート

```ts
figma.createRectangle()
figma.closePlugin()
```

---

# Figmaの Plugin API

今回はモーダルを表示して操作できるようにしたい

`showUI`: ユーザーが対話的に操作できたり、ブラウザのAPIにアクセスするためのUIをレンダリングできる

引数 `html` に指定されたHTMLを含む `<iframe>` モーダルを作成する

```ts
showUI(html: string, options?: ShowUIOptions): void
``` 

---

# Figmaの Plugin API

使用例

```ts
figma.showUI(
	"<p>Hello Figma<p>",
	{ width: 400, height: 200, title: "Tutorial" }
)
```

<img src="/plugin4.png" alt="" style="width: 500px; margin: 0 auto">

---

# Smart Animate

- プロトタイプのフレーム間でマッチングレイヤーを検知し、違いを認識し、レイヤーにアニメーションを付けられる
  - スクロール時のパララックス効果
  - ドラッグ、スワイプ、長押しなどのタッチジェスチャー
  - スライダー、トグル、スイッチ

---

# Smart Animate

<div style="width: 300px; margin: 0 auto">
<Tweet id="1179795593180438543" />
</div>

---

# Smart Animate

使用方法

- フレームを二つ用意する
- 同名かつ同種類のレイヤーを作成し、各フレームの始点終点へ配置する
- 「Prototype」タブから始点 => 終点とフレームを繋げる
- 「Interactions」をクリック、「Animation」下部のセレクト部分から「Smart Animate」を選択

---

# Smart Animate

サポートされているプロパティ

- Scale（大きさ）
- Position（位置）
- Opacity（不透明度）
- Rotation（回転）
- Fill（塗り）

---

# 動作確認

いざ実演

---

# まとめ

- TypeScript さえあれば意外と簡単にPluginは作れる
- Figmaのアニメーションはすごい

---

# 参照

- https://www.figma.com/plugin-docs/api/api-reference/
- https://www.lekoarts.de/javascript/creating-a-figma-plugin-with-svelte
- https://ics.media/entry/210526/