# Metis 設計レビュー資料

このリポジトリは、卒業制作 **Metis** の先生レビュー用に、元リポジトリの設計資料を抜粋・再構成したものです。

## 方針

元の `5B-Projects/metis` には、画面遷移、画面レイアウト、コンポーネントツリー、画面データ要求、ER、Supabase配置、DBスキーマ、ADRなどが広く含まれています。

このレビュー用サイトでは、特に **`docs/design/05-component-tree` 以降の設計図** を中心に見せます。

## 先生に最初に見てほしい順番

1. [05 コンポーネントツリー図](05-component-tree.md)
2. [06 画面データ要求](06-screen-data-requirements.md)
3. [07 ER図](07-er.md)
4. [09 Supabase配置](09-supabase-data-placement.md)
5. [10 DBスキーマ設計](10-database-schema.md)

## コア機能の範囲

Metis の中核は以下です。

```text
ゴール入力
  ↓
ガイド生成
  ↓
ガイド進行
  ↓
AI相談・評価
  ↓
教材化
  ↓
教材閲覧・学習
```

初回レビューでは、課金・管理者・監査ログ・組織管理は補助扱いにします。

## GitHub Pages について

このサイトは MkDocs で生成し、GitHub Actions から GitHub Pages へ公開する想定です。

画像は `5B-Projects/metis` から Actions 実行時にコピーします。Private リポジトリ間で画像を取得するため、`review_Repository` 側に `METIS_REPO_TOKEN` secret を設定してください。
