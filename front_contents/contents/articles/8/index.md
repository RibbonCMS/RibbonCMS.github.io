---
description: 'この記事では RibbonCMS の初回セットアップ実行している `tie.sh` というシェルスクリプトについて、何をやっているのかをコードベースで解説します（実は、初回セットアップのほとんどはこの
  `tie.sh` で行われています）。  `tie.sh` のコードはこちらで確認できます:  > https://github.com/RibbonCMS/RibbonCMS/blob/main/...'
posted_at: 2023-07-25 16:15:53+00:00
slug: '8'
tag_ids:
- 5766700229
title: セットアップで実行している `tie.sh` は何をやっているのか？
updated_at: 2023-07-30 05:42:21+00:00

---
この記事では RibbonCMS の初回セットアップ実行している `tie.sh` というシェルスクリプトについて、何をやっているのかをコードベースで解説します（実は、初回セットアップのほとんどはこの `tie.sh` で行われています）。

`tie.sh` のコードはこちらで確認できます:

> https://github.com/RibbonCMS/RibbonCMS/blob/main/tie.sh

## `tie.sh` を実行する前に…

`tie.sh` を実行する前にまず private リポジトリの新規作成と `tie.sh` のダウンロードが必要です。次のセットアップページをもとに `tie.sh` を実行する前まで手順を完了させてください:

<!-- [RibbonCMS のセットアップ | RibbonCMS](https://ribboncms.github.io/articles/5) -->

#5 

## STEP 1 ｜ RibbonCMS/RibbonCMS の複製

```
  git clone --bare git@github.com:RibbonCMS/RibbonCMS.git ./$REPO_NAME.git
  cd ./$REPO_NAME.git
  git push --mirror git@github.com:$USER_NAME/$REPO_NAME.git
```

ここでは先ほど作成した自分用 CMS リポジトリへ[RibbonCMS/RibbonCMS](https://github.com/RibbonCMS/RibbonCMS)の完全なコピー（ファイルだけでなくブランチやタグ等も含むリポジトリ自体の複製）を作成しています。

`--bare` や `--mirror` オプションについては次の記事をご覧ください:

> 参考: [git clone 時の mirror と bare の違い | exMedia](https://www.exmedia.jp/blog/git-clone%E6%99%82%E3%81%AEmirror%E3%81%A8bare%E3%81%AE%E9%81%95%E3%81%84/)

なお、 fork ではなくリポジトリの複製という方法を採っている理由は、 fork だと issue の作成が行えないためです。

## STEP 2 ｜ローカルリポジトリの削除

```
  cd ..
  rm -rf ./$REPO_NAME.git
```

先ほどの push が完了したら、もうローカルリポジトリは不要なので削除しておきます。

## STEP 3 ｜ 自分用 CMS リポジトリを clone

```
  git clone git@github.com:$USER_NAME/$REPO_NAME.git
  cd ./$REPO_NAME
```

## STEP 4 ｜環境設定ファイルを作成

```
  GITHUB_USER_NAME=$(git config user.name)
  GITHUB_USER_EMAIL=$(git config user.email)
  echo "#!/bin/bash" >| ./settings.conf
  echo "GITHUB_USER_NAME='$GITHUB_USER_NAME'" >> ./settings.conf
  echo "GITHUB_USER_EMAIL='$GITHUB_USER_EMAIL'" >> ./settings.conf
  echo "FRONTEND_REPOSITORY='$SIDE_F_REPO'" >> ./settings.conf
  echo "SIDE_M_REPOSITORY='$SIDE_M_REPO'" >> ./settings.conf
```

RibbonCMS の環境設定ファイルを作成しています。処理としては `settings.conf` に次の 4 つの環境変数を記述しています。

| 環境変数              | 役割                                                                                                     |
| --------------------- | -------------------------------------------------------------------------------------------------------- |
| `GITHUB_USER_NAME`    | GitHub のユーザ名を格納します                                                                            |
| `GITHUB_USER_EMAIL`   | GitHub に登録されているメールアドレスを格納します                                                        |
| `FRONTEND_REPOSITORY` | sideF に利用するテンプレートのリポジトリ名を格納します（デフォルトでは `RibbonCMS/SimpleBlogTemplate` ） |
| `SIDE_M_REPOSITORY`   | sideM に利用するテンプレートのリポジトリ名を格納します（デフォルトでは `RibbonCMS/RibbonCMS_sideM` ）    |

`settings.conf` の内容はこの後実行する `setup.sh` で使います。

## STEP 5 ｜セットアップスクリプトを実行

```
  ./setup.sh
```

セットアップスクリプト `setup.sh` を実行しています。コードはこちらから確認できます:

> https://github.com/RibbonCMS/RibbonCMS/blob/main/setup.sh

`setup.sh` で行っていることは主に次の通りです:

- 記事投稿用 GitHub Actions ワークフロー `issue_builder.yml` の作成
- sideF リポジトリに用意されている issue テンプレート（記事投稿用など）を自分用 CMS リポジトリの `ISSUE_TEMPLATE/` へコピー

`issue_builder.yml` の作成では次の処理を行っています:

1. `issue_builder.yml` のテンプレートである `templates/workflows/issue_builder.yml` に先ほど作成した `settings.conf` の内容を反映
1. 反映した `issue_builder.yml` を自分用 CMS リポジトリの`.github/workflows/`に配置

`setup.sh` 内の該当コードを下記に示します:

```
source ./settings.conf

issue_builder='./templates/workflows/issue_builder.yml'
workflow=$(cat $issue_builder)
workflow=$(echo "$workflow" | sed -e "s|T_GITHUB_USER_NAME|${GITHUB_USER_NAME}|g")
workflow=$(echo "$workflow" | sed -e "s|T_GITHUB_USER_EMAIL|${GITHUB_USER_EMAIL}|g")
workflow=$(echo "$workflow" | sed -e "s|T_FRONTEND_REPOSITORY|${FRONTEND_REPOSITORY}|g")
workflow=$(echo "$workflow" | sed -e "s|T_SIDE_M_REPOSITORY|${SIDE_M_REPOSITORY}|g")
if [ ! -d ./.github/workflows ]; then
    mkdir -p ./.github/workflows
fi
if [ -e ./.github/workflows/issue_builder.yml ]; then
    read -p "cp: overwrite './.github/workflows/issue_builder.yml'? " prompt;
    if [ $prompt = 'y' ]; then
        echo "$workflow" >| ./.github/workflows/issue_builder.yml
    fi
else
    echo "$workflow" >| ./.github/workflows/issue_builder.yml
fi
```

`templates/workflows/issue_builder.yml` の中身はこちら:

> https://github.com/RibbonCMS/RibbonCMS/blob/main/templates/workflows/issue_builder.yml

`ISSUE_TEMPLATE/` へのコピーでは次の処理を行っています:

1. sideF リポジトリを clone
1. sideF リポジトリの `templates/issues/` 内にある issue テンプレートを自分用 CMS リポジトリの `ISSUE_TEMPLATE/` へコピー

どのような issue テンプレートがあるかは sideF リポジトリによって異なります。例えば、 FunctionalBlogTemplate には次のような issue テンプレートが用意されています:

> https://github.com/RibbonCMS/FunctionalBlogTemplate/tree/main/templates/issues

## STEP 6 ｜これまでの変更を自分用 CMS リポジトリへ反映

```
  git add .
  git commit -m 'exec first setup.sh'
  git push origin main
```

以上が `tie.sh` が行っている内容です。


