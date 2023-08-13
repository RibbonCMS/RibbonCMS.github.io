---
description: 'RibbonCMS では用意されている [フロントエンドテンプレート](https://ribboncms.github.io/articles/7)
  を利用する以外に自作することもできます。  ## VanillaTemplate をベースにしよう  その場合、次の [RibbonCMS/VanillaTemplate](https://github.com/RibbonCMS/VanillaT...'
posted_at: 2023-08-13 17:05:24+00:00
slug: '10'
tag_ids:
- 5766700229
title: RibbonCMS のフロントエンドを自作する
updated_at: ''

---
RibbonCMS では用意されている [フロントエンドテンプレート](https://ribboncms.github.io/articles/7) を利用する以外に自作することもできます。

## VanillaTemplate をベースにしよう

その場合、次の [RibbonCMS/VanillaTemplate](https://github.com/RibbonCMS/VanillaTemplate) をベースに作り始めることをオススメします。

VanillaTemplate は RibbonCMS の機能のみを実装し、デザイン面はほとんど素の HTML のままになっているテンプレートです。このテンプレートをベースにすることで RibbonCMS の機能を実装する工数を削減し、デザイン面のカスタマイズに注力することができます。

> [RibbonCMS/VanillaTemplate](https://github.com/RibbonCMS/VanillaTemplate)

## 0 から作りたい場合

VanillaTemplate を使わず 0 からフロントエンドを自作したい場合は次のポイントに注意してください。

なお、ライブラリは Next.js を利用しています。

### Next.js プロジェクト作成時

`create-next-app` 時の質問は次の質問だけ次のように回答してください:

```
$ docker compose run --rm -u node node npx create-next-app@latest
～～（略）～～
Would you like to use `src/` directory? Yes
Would you like to use App Router? (recommended) No   # Webサイトを作るのは No にした方がやりやすいため
～～（略）～～
```

プロジェクト作成が完了したら、プロジェクトディレクトリの名前を `src` へ変更してください。なぜなら、 sideM で生成したファイルのコピー先のパスに `front/src/` が含まれているからです（ [→ 該当箇所](https://github.com/RibbonCMS/RibbonCMS_sideM/blob/main/models/consts.py#L89) ）

```
mv ./<PROJECT_DIR>/ ./src/
```

### `next.config.js`に加える変更

`next.config.js` の `basePath` に `lib/consts.js` で定義されている `basePath` を指定する必要があります（ `lib/consts.js` は sideM で自動生成されます）。

これをしておかないと、リポジトリ名が `<YOUR_GITHUB_USER_NAME>.github.io` 以外の場合に Next.js の`<Link>`の`href`のパスの basePath が`https://<YOUR_GITHUB_USER_NAME>.github.io/<YOUR_REPO_NAME>`ではなく`https://<YOUR_GITHUB_USER_NAME>.github.io`になってしまい、意図した箇所にリンクできません。

```js
const { basePath } = require('./lib/consts')

const nextConfig = {
～～（中略）～～
  basePath: basePath,
}
```

### 固定ページを作る場合の推奨仕様

sideM では、固定ページで利用する JSON ファイルは`contents/fixed`へコピーされる仕様になっています（ [→ 該当箇所](https://github.com/RibbonCMS/RibbonCMS_sideM/blob/main/models/consts.py#L96) ）。

VanillaTemplate では、固定ページを作成しやすいよう下記の仕様に沿って実装を進めています。0 からフロントエンドを自作する場合もこの仕様に沿って実装することをオススメします。

新しく固定ページを作りたい場合は `components/pages/fixed/` へ固定ページごとにディレクトリを作ってそこへ実装していきましょう。

実装する必要があるものは次のとおりです:

- 固定ページ用のコンポーネントファイル
- sideM から渡された固定ページ用 JSON ファイルの型やデフォルト値などを定義するファイル（JSON 定義ファイル）

JSON 定義ファイルで定義する必要があるものは次の通り:

- JSON ファイルのパス（ `contents/fixed/<YOUR_FIXED_PAGE_JSON>.json` ）
- JSON の中身がどうなっているかの型定義
- 型のデフォルト値

VanillaTemplate での実装例を次に示します:

> https://github.com/RibbonCMS/VanillaTemplate/tree/main/src/components/pages/fixed/home


