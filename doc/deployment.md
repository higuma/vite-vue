# GitHub Pagesへのデプロイ

Vite環境からGitHub PagesへのデプロイはGitHub Actionsを使うとよい。

https://ja.vitejs.dev/guide/static-deploy.html#github-pages

> 従来の`gh-pages`ブランチを使う方法も可能。ただし今ではActionsを使う方がずっと楽で、一回設定しておけば後はgit pushだけで自動処理できる。

## 設定

Viteドキュメントに書かれている通りにする。まず`vite-config.js`に`base`を設定。

```
export default defineConfig({
  plugins: [vue()],
  base: '/<repo-name>/',
})
```

次にrepoの Settings - Pages ページを表示し、Web上で作業する。

- GitHub Pages
  - Build and deployment
    - (Deploy from a branch) ⇒ GitHub Actions
  - templateが2種類(GitHub Pages Jekyll, Static HTML)ある
    - Static HTMLのConfigureボタンを押してテンプレートを表示
      - 後ろから4行目の`path: '.'`を`path: `./dist'`に変更
      - オンライン編集でcommitした方が確実(.github/workflows.static.yml)
- 終わったらローカル環境へgit pull
- `.gitignore`をチェックし、`dist`が登録されていたら削除(これを忘れると反映されない)

> (参考 - 作業記録): `.github/workflows.static.yml`をローカル側に作成し、編集してからpushする方法も試みたがうまくいかなかった。上記のようにオンラインで編集＋コミットし、そこからpullしたらすんなりできた。

## 操作法

Vite環境での作業は次の通り。まずproduction buildする。

```
$ npm run build
```

結果確認にはpreviewを用いる。

```
$ npm run preview
```

> これは`dev`とは異なり、buildした`./dist/*`に対する純粋なローカルサーバーを起動する。

設定はこれで終了。後はcommitしてpushすればよい。

```
$ git add .
$ git commit -m '...'
$ git push -u origin main
```

結果はrepoページからActionをクリックして確認用ページでチェックする。すぐには反映されず、数十秒から数分掛かることもあるのでしばらく待つこと。

## ドキュメント

基礎編。pushに対して起動する。

https://docs.github.com/ja/actions/quickstart

もっと詳しい解説(参考)。

https://docs.github.com/ja/actions/learn-github-actions/understanding-github-actions

## 課金

大規模開発でなければ無料プラン内で利用できるはずだが、ここはきちんと調べる。次に書かれている。

https://docs.github.com/ja/billing/managing-billing-for-github-actions/about-billing-for-github-actions

次は検索で見つけた。こちらの方が分かりやすい。

https://docs.github.com/ja/billing/managing-your-github-billing-settings/about-billing-on-github

Storage(上限500MB)はアプリ用repoであればまず問題なく収まる。ランナーの実行時間は2000分/月まで無料。実際に費やした時間はActionsページで確認できる(長くとも1回のcommitで30秒程度)。単純に概算すると毎月4000コミット程度までは無料なので、まず超過することはないだろう。

> 累積時間を表示する機能はまだ付いてないらしい(現在ActionsはBeta運用状態)。

今はまず気にしなくていいが、利用頻度が高くなったら次を見ること。[料金計算ツール](https://github.com/pricing/calculator?feature=actions)で見積もる。

https://docs.github.com/ja/billing/managing-billing-for-github-actions/about-billing-for-github-actions#calculating-minute-and-storage-spending

(参考) もし課金が必要になったらSettingsに入力する。

- https://github.com/settings/billing/summary
- https://github.com/settings/billing/spending_limit
- https://github.com/settings/billing/payment_information
