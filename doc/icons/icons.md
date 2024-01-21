# アイコンの設定

## 目的

Webサイトに用いるアイコン画像セット一式を作成し、それらをHTMLに設定する。

## リファレンス

次を読みながら進める。

https://coliss.com/articles/build-websites/operation/work/how-to-favicon.html

> (参考)原文は次の通り。ただし上記ドキュメントは単に和訳だけではなく、原著には書かれていないツールの紹介や使用法なども追加されている(原文よりもさらに有用)。

https://evilmartians.com/chronicles/how-to-favicon-in-2021-six-files-that-fit-most-needs

## SVGの作成

元となるSVG画像を作成する。今回は[Inkscape](https://inkscape.org/)を用い、320ｘ320(px)のSVG画像を作成した。元画像は次の通り。

[![bear.svg](bear.svg)](bear.svg)

> 下手くそ(ごめん)。お絵描きが目的ではなく、それをどのようにWeb用アイコンとして設定するかという練習用なのでどうか許して。

## 各種環境への対応

元画像のSVGから各種画像ファイルを生成して対応する。今回はビットマップ画像生成ツールとして[GIMP](https://www.gimp.org/)を用いた。

### モダンブラウザ

モダンブラウザはSVGアイコンに対応しており、元のSVG画像をそのまま用いてもよい。ただしInkscapeなどの画像アプリが生成するSVGには編集用の情報も含まれており、最小限必要なサイズよりもかなり大きくなっている場合が多い。

画像アプリで作られたSVGから編集用情報を除去し、表示専用に最適化するには[svgo](https://github.com/svg/svgo)を用いるとよい。

```
$ npm install -g svgo
```

実行例を示す(75.8%の削減!)。コマンド順序に注意。入力ファイル(`bear.svg`)が先で出力指定(`-o icon.svg`)を後に指定する。

```
$ svgo --multipass bear.svg -o icon.svg

bear.svg:
Done in 55 ms!
13.746 KiB - 75.8% = 3.332 KiB
```

HTMLには次のように設定する。

```html
<link rel="icon" href="/icon.svg" type="image/svg+xml">
```

### レガシーブラウザ

元画像をGIMPで取り込み、32x32に縮小して`favicon.ico`としてexportする。

[![favicon.ico](favicon.ico)](favicon.ico)

これをHTMLヘッダへ設定する。

```html
<link rel="icon" href="/favicon.ico" sizes="any">
```

### Android

Androidデバイスでは[プログレッシブWebアプリ](https://developer.mozilla.org/ja/docs/Glossary/Progressive_web_apps)の仕様を適用し、[Webアプリマニフェスト](https://developer.mozilla.org/ja/docs/Web/Manifest)(具体的には`manifest.json`)に設定する。

まずSVGをビットマップツールに取り込み、スケールして512x512と192x192のPNG画像ファイルを生成する。

[![icon-512.png](icon-512.png)](icon-512.png)

[![icon-192.png](icon-192.png)](icon-192.png)

次にマニフェストを設定する。まずHTML側に参照するマニフェストファイル(`manifest.json`)を指定する。

```html
link rel="manifest" href="/manifest.json">
```

`manifest.json`には次のように記述する(アイコン設定部のみ)。

```json
{
  ...(略)...
  "icons": [
    { "src": "/icon-192.png", "type": "image/png", "sizes": "192x192" },
    { "src": "/icon-512.png", "type": "image/png", "sizes": "512x512" }
  ]
}
```

### apple-touch-icon

Appleのデバイスで用いられる。詳しい解説は次を参考にすること。必須ではないが、iOS環境からアクセスした場合は必ずこれを参照するため、設定しないと内部で大量の404エラーが発生する(表には出ないがアクセス遅延の要因になる)。

https://www.homepage-tukurikata.com/hp/apple-touch-icon.html

基本的には180x180に変換して`apple-touch-icon.png`として保存すればよい(最低限の対応)。これでiOSユーザーがそのWebページを「ホーム画面に追加」すると「Webクリップ」アイコンとして登録される。

次のような画像を作成し、`apple-touch.icon.png`として保存する。

- できあがり寸法は180x180(px)で背景付き
- 元画像を140x140にスケールしたビットマップ画像を作成
- 周囲に20pxのパディングを確保
- 単色(何色でもよい)の背景と重ねてPNGを生成

[![apple-touch-icon.png](apple-touch-icon.png)](apple-touch-icon.png)

これをHTMLに次のように設定する。

```html
<link rel="apple-touch-icon" href="/apple-touch-icon.png">
```

## ファイルの設定

以上作成した各種アイコンファイル(manifestも含む)はサイトルートに配置する。Viteでは静的assetsとして`public/`にコピーすればよい。

https://ja.vitejs.dev/guide/assets.html#the-public-directory
