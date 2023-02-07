---
description: '## 記事の投稿  1. Issues ページから Article テンプレートを用いて Issue を作成します 2. YAML フォーマットの下に記事を書きます
  3. Issue の編集を完了し， `publish` ラベルをつけます  ラベルをつけると GitHub Actions が実行されます．実行には 1 分程度かかります．実行完了後，サイトにアクセスし，投稿した記事が表示されていれば...'
posted_at: 2023-02-07 07:57:35+00:00
slug: '6'
tag_ids:
- 5125441130
title: 記事の投稿/編集/削除
updated_at: 2023-02-07 08:11:27+00:00

---
## 記事の投稿

1. Issues ページから Article テンプレートを用いて Issue を作成します
2. YAML フォーマットの下に記事を書きます
3. Issue の編集を完了し， `publish` ラベルをつけます

ラベルをつけると GitHub Actions が実行されます．実行には 1 分程度かかります．実行完了後，サイトにアクセスし，投稿した記事が表示されていれば OK です．

💡 記事のサムネイルや予約投稿などは Issue 本文上部の YAML フォーマットで設定できます
💡 `tag/xx` というラベルを作成すると，記事にタグをつけることができます

## 記事の編集

1. 編集したい記事の Issue から `publish` ラベルを外します
2. Issue を編集します
3. 編集が完了したら `publish` ラベルをつけ， GitHub Actions の完了を待ちます．

## 記事の非公開

1. 削除したい記事の Issue に `delete` ラベルをつける， GitHub Actions の完了を待ちます

## 記事の再公開

1. 再公開したい記事の Issue から `delete` ラベルを外します
2. `publish` ラベルをつけ， GitHub Actions の完了を待ちます

## 記事の削除

1. 削除したい記事の Issue を削除し， GItHub Actions の完了を待ちます
