---
description: '1. 新しい空の **private** リポジトリを作成します（ public リポジトリで作成してしまうと，誰でも CMS へアクセスできてしまいます）  💡
  リポジトリ名はサイト URL で使われます  2. 次のコマンドを実行して `tie.sh` をダウンロードします（ `tie.sh` は RibbonCMS
  をセットアップするためのシェルスクリプトです）:  ``` $ curl h...'
posted_at: 2023-02-07 07:42:12+00:00
slug: '5'
tag_ids:
- 5125441130
- 5766699763
title: RibbonCMS のセットアップ
updated_at: 2023-07-23 17:38:55+00:00

---
1. 新しい空の **private** リポジトリを作成します（ public リポジトリで作成してしまうと，誰でも CMS へアクセスできてしまいます）

💡 リポジトリ名はサイト URL で使われます

2. 次のコマンドを実行して `tie.sh` をダウンロードします（ `tie.sh` は RibbonCMS をセットアップするためのシェルスクリプトです）:

```
$ curl https://raw.githubusercontent.com/RibbonCMS/RibbonCMS/main/tie.sh > tie.sh
```

3. `tie.sh` を実行して RibbonCMS をセットアップします． `tie.sh` の使い方は次の通りです:

```
usage: ./tie.sh [--side-f SIDE_F_REPO] [--side-m SIDE_M_REPO] user_name repo_name
```

`tie.sh` には次の 4 つの引数があります:

- `--side-f SIDE_F_REPO`
    - RibbonCMS のフロントエンドに使用するテンプレートを指定できます
    - この引数を省略した場合， [`RibbonCMS/SimpleBlogTemplate`](https://github.com/RibbonCMS/SimpleBlogTemplate) が使用されます．
- `--side-m SIDE_M_REPO`
    - RibbonCMS のバックエンドに使用するテンプレートを指定できます
    - この引数を省略した場合， [`RibbonCMS/RibbonCMS_sideM`](https://github.com/RibbonCMS/RibbonCMS_sideM) が使用されます．
- `user_name`
    - 自分の GitHub ユーザネームを指定します
    - **この引数は省略できません**
- `repo_name`
    - 作成したリポジトリの名前を指定します
    - **この引数は省略できません**

例えば，この RibbonCMS ホームページでは `tie.sh` の引数を次のように設定しました:

```
$ ./tie.sh --side-f RibbonCMS/FunctionalBlogTemplate --side-m RibbonCMS/RibbonCMS_sideM RibbonCMS RibbonCMS.github.io
```

💡 `tie.sh` 実行時に `permission denied: ./tie.sh` と出る場合， `chmod` コマンドなどを用いて `tie.sh` の実行権限を取得してください

4. 作成したリポジトリの Labels ページへ移動し，次のラベルを追加してください（元々あったラベルは削除してもかまいません）:

- `publish` : 記事を投稿するために使用するラベルです
- `article` : 記事を執筆するために使用するラベルです．このラベルのついた Issue は「記事」として認識されます
- `config` : サイト全体の設定を行うために使用するラベルです．このラベルの着いた Issue は「サイト設定」として認識されます
- `delete` : 記事を非公開にするために使用するラベルです

💡 使用するテンプレートによっては `fixed/xx` といったラベルを別途追加する必要があります
💡 `tag/xx` というラベルを追加すると，記事にタグが付けられます

5. Issues ページから Config テンプレートを用いて Issue を作成し，サイト全体の設定を行います．設定項目は使用するテンプレートによって異なります．例えば `RibbonCMS/SimpleBlogTemplate` の設定項目およびその初期値は次の通りです:

```yml
blog_title: Simple Blog Template
site_introduction: This is template of RibbonCMS
copylight_name: ShotaroKataoka
copylight_url: https://github.com/ShotaroKataoka
root_url: https://ribboncms.github.io/SimpleBlogTemplate
issues_page_url: https://github.com/RibbonCMS/RibbonCMS_sideM/issues
favicon_image_url: https://user-images.githubusercontent.com/42331656/175183205-06dc6033-af20-44b9-ae3d-c3e68770a1f3.png
author_name: RibbonCMS team
author_introduction: We are RibbonCMS team.
```

これら設定項目の中で，**この手順で確実に変える必要のある項目** は次の通りです:

- `root_url`
    - リポジトリ名によって設定値が異なります:
        - リポジトリ名が `<your_user_name>.github.io` の場合:
            - `https://<your user name>.github.io`
        - それ以外の場合:
            - `https://<your user name>.github.io/<Your repository name>`
- `issues_page_url`
    - リポジトリの Issues ページの URL を設定してください

設定できたら， Issue の編集を完了して `publish` ラベルをつけてください（ラベルをつけると GitHub Actions が実行されます．実行完了までは 1 分ほどかかります）

6. リポジトリの Settings -> Pages -> Source branch を次のように設定してください: `build` / `(root)`

以上の手順を完了し， `root_url` に指定した URL にアクセスしてサイトが表示されていれば RibbonCMS のセットアップは完了です！

