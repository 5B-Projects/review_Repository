# 05 コンポーネントツリー図

## 位置づけ

`docs/design/05-component-tree/` は、画面ごとの React UI コンポーネントツリー図を管理する設計図です。

元リポジトリでは、`.puml` を正本、`image/` 配下の PNG をレンダリング結果として扱います。

!!! note "レビューで見るポイント"
    - 画面レイアウトを実装へ移すとき、どの粒度でコンポーネント化するか。
    - `[shared]` の共通化候補が妥当か。
    - コア画面である SC006〜SC009 の情報密度が過剰・不足していないか。

## コア画面

| 画面ID | 画面 | レビュー観点 |
|---|---|---|
| SC002 | ホーム | 新規学習開始・学習再開・おすすめ教材への導線 |
| SC006 | ガイド生成 | ゴール設定、タグ選択、生成中表示 |
| SC007 | ガイド進行 | 章・ステップ本文、AI相談、評価、進捗 |
| SC008 | 学習ガイド終了 | 完了結果、AI評価、教材化導線 |
| SC009 | 教材詳細 | 教材閲覧、学習開始、レビュー、購入導線 |

## コア画面のコンポーネントツリー

### SC002 ホーム

![ホーム](assets/component-tree/home-page.png)

### SC006 ガイド生成

![ガイド生成](assets/component-tree/learning-guide-generation-page.png)

### SC007 ガイド進行

![ガイド進行](assets/component-tree/guide-progress-page.png)

### SC008 学習ガイド終了

![学習ガイド終了](assets/component-tree/guide-completion-page.png)

### SC009 教材詳細

![教材詳細](assets/component-tree/material-detail-page.png)

## 補助画面のコンポーネントツリー

初回レビューでは補助扱いですが、画面全体の整合性確認用に掲載します。

### SC001 認証

![認証](assets/component-tree/auth-page.png)

### SC003 マイページ

![マイページ](assets/component-tree/my-page-page.png)

### SC004 設定

![設定](assets/component-tree/settings-page.png)

### SC005 カート

![カート](assets/component-tree/cart-page.png)

### SC011 サブスクリプションプラン

![サブスクリプションプラン](assets/component-tree/subscription-plan-page.png)

### SC012 決済結果

![決済結果](assets/component-tree/payment-result-page.png)

### SC013 管理者ダッシュボード

![管理者ダッシュボード](assets/component-tree/admin-dashboard-page.png)

### SC014 管理者向けユーザー詳細

![管理者向けユーザー詳細](assets/component-tree/admin-user-detail-page.png)

### SC015 管理者向け教材詳細

![管理者向け教材詳細](assets/component-tree/admin-material-detail-page.png)

### SC016 取引履歴詳細

![取引履歴詳細](assets/component-tree/admin-transaction-detail-page.png)

### SC017 監査ログ

![監査ログ](assets/component-tree/audit-log-page.png)
