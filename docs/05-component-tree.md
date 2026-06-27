# 05 コンポーネントツリー図

## この設計図の目的

`docs/design/05-component-tree/` は、画面を React で実装するときに、UIをどの程度の大きさの部品へ分けるかを整理する設計図です。

このレビュー資料では、元資料の全画像をそのまま並べるのではなく、**コア画面の分割方針が分かる説明** と **代表画面のツリー図** に絞って掲載します。

画像は `review_Repository` の `docs/assets/component-tree/` に直接含めています。  
そのため、GitHub Actions 実行時に別リポジトリから画像をコピーする必要はありません。

!!! note "画像の確認方法"
    ページ上の画像はクリックすると拡大表示できます。  
    画像直下のリンクから、画像ファイル単体やGitHub上の実ファイルも確認できます。

---

## コンポーネント分割のざっくり粒度

レビューでは、細かいボタンやラベル単位ではなく、以下の粒度で見ます。

| 粒度 | 例 | レビューで見ること |
|---|---|---|
| レイアウト単位 | `UserLayout`, `AdminLayout`, `AppHeader`, `AdminHeader` | 画面共通のヘッダーや枠組みを共通化できているか |
| ページ単位 | `HomePage`, `GuideProgressPage`, `MaterialDetailPage` | 1画面の責務が大きすぎないか |
| セクション単位 | `GoalInputSection`, `ChapterContentArea`, `ReviewSummarySection` | 画面内の情報のまとまりが自然か |
| パネル・カード単位 | `AiAssistantDrawer`, `ProgressCard`, `MaterialSummaryCard` | 表示情報が分かりやすく分かれているか |
| モーダル・ドロワー単位 | `ConfirmDialog`, `AiDrawer`, `AdminActionPanel` | 必要なときだけ開く情報になっているか |

この段階では、`Button`、`Input`、`Label`、`Icon` のような細かいUI部品まではレビュー対象にしません。

---

## 共通コンポーネントの考え方

ツリー図では `[shared]` が付いたノードを、複数画面で再利用する候補として扱います。

| 共通候補 | 用途 | 見るポイント |
|---|---|---|
| `UserLayout [shared]` | 認証画面以外の利用者向け画面の共通レイアウト | ヘッダー、検索、カート、マイページ導線が共通化できるか |
| `AdminLayout [shared]` | 管理者向け画面の共通レイアウト | 管理者ヘッダー、監査ログ導線、ユーザー画面へ戻る導線が共通化できるか |
| `PageTitleBlock [shared]` | ページタイトル・説明文 | 画面ごとの目的が最初に分かるか |
| `AiAssistantDrawer [shared]` | AI相談パネル | ガイド進行、教材閲覧、コード評価で再利用できるか |
| `JobStatusPanel [shared]` | 非同期処理の進捗表示 | 生成、評価、エクスポートなどで状態表現を揃えられるか |

!!! warning "注意"
    `[shared]` は「実装時に必ず共通化する」という意味ではなく、共通化候補の目印です。  
    実装時に、props・状態管理・CSS・アクセシビリティを見て最終判断します。

---

## コア画面ごとの分割方針

### SC006 ガイド生成

ガイド生成画面は、利用者が「作りたいもの」を入力し、タグや学習方針を選び、AI生成を開始する画面です。

| 領域 | 目的 | 対応する主なデータ |
|---|---|---|
| ゴール入力領域 | 利用者が作りたいものを入力する | `goal_drafts` |
| テンプレート選択領域 | 初学者が入力に迷わないように例を出す | テンプレート定義、下書き反映 |
| タグ選択領域 | 使用技術や学習対象を選ぶ | `technology_tags`, 選択タグ |
| 学習スタイル設定領域 | 難易度・説明粒度・学習方向を調整する | `learning_style_defaults`, 生成時スナップショット |
| 構成案確認領域 | 生成前にガイド構成を確認する | `guide_outline_versions` |
| 生成進捗領域 | AI生成中の状態、失敗、再試行を表示する | `jobs`, `generation_jobs`, `guardrail_results` |

レビュー観点は、**入力 → 確認 → 生成 → 学習開始** の流れが迷わないかです。

### SC007 ガイド進行

ガイド進行画面は、学習本文、進捗、AI相談、コード評価が集まるため、情報量が多くなりやすい画面です。

| 領域 | 目的 | 対応する主なデータ |
|---|---|---|
| 章・ステップ一覧 | 現在位置と進捗を確認する | `guide_chapters`, `guide_steps`, `guide_progress` |
| 本文表示領域 | 学習内容を読む | `guide_chapters`, `guide_steps` |
| コードブロック領域 | コード例やコピー操作を扱う | 本文内コード、コピー操作ログ |
| 完了チェック領域 | ステップ完了、次章への進行を扱う | `step_progress`, `chapter_progress` |
| AI相談パネル | 質問・回答・文脈付き相談を扱う | `chat_messages`, `ai_executions` |
| コード評価領域 | 実行評価・品質評価・改善提案を扱う | `evaluation_jobs`, `evaluation_results` |

レビュー観点は、**学習本文とAI相談が混ざりすぎていないか**、**進捗更新と評価が自然につながるか**です。

### SC008 学習ガイド終了

ガイド終了画面では、完了結果・AI評価・改善提案・教材化導線を分けて見せます。

| 領域 | 目的 | 対応する主なデータ |
|---|---|---|
| 完了サマリー | 完了したガイド、学習時間、進捗率を表示する | `guide_completions` |
| AI評価サマリー | 総合評価と評価コメントを表示する | `ai_evaluation_summaries` |
| 改善提案 | 次に伸ばすポイントを表示する | `evaluation_results` |
| 教材化導線 | 完了済みガイドから教材作成へ進む | `material_creation_jobs` |
| 復習導線 | ガイドに戻って復習する | `guides`, `guide_progress` |

レビュー観点は、**完了した成果が分かるか**、**教材化へ進む条件が明確か**です。

### SC009 教材詳細

教材詳細画面は、教材の閲覧、購入、レビュー、作者操作が集まりやすい画面です。

| 領域 | 目的 | 対応する主なデータ |
|---|---|---|
| 教材概要 | タイトル、説明、価格、作者、評価を表示する | `materials`, `material_versions` |
| 学習内容・目次 | 教材の章やレッスン構成を表示する | `material_sections`, `material_chapters` |
| 購入・学習開始導線 | 未購入・購入済み・作者で表示を切り替える | `entitlements`, `orders` |
| レビュー領域 | 評価・コメント・レビュー集計を表示する | `material_reviews`, 集計値 |
| 作者向け操作 | 公開状態、作者メモ、エクスポートなどを扱う | `materials`, `export_jobs` |

レビュー観点は、**教材閲覧者・購入済み利用者・作者の表示分岐が過不足ないか**です。

---

## ツリー図の例

### 例1: SC006 ガイド生成

この図は、ゴール入力・タグ選択・学習スタイル設定・生成状態表示をどう分けるかを見るための例です。

[![SC006 ガイド生成 コンポーネントツリー](../assets/component-tree/learning-guide-generation-page.svg)](../assets/component-tree/learning-guide-generation-page.svg)

画像ファイル: [サイトで開く](../assets/component-tree/learning-guide-generation-page.svg) / [GitHubで確認](https://github.com/5B-Projects/review_Repository/blob/main/docs/assets/component-tree/learning-guide-generation-page.svg)

### 例2: SC007 ガイド進行

この図は、学習本文・進捗・AI相談・コード評価をどう分けるかを見るための例です。

[![SC007 ガイド進行 コンポーネントツリー](../assets/component-tree/guide-progress-page.svg)](../assets/component-tree/guide-progress-page.svg)

画像ファイル: [サイトで開く](../assets/component-tree/guide-progress-page.svg) / [GitHubで確認](https://github.com/5B-Projects/review_Repository/blob/main/docs/assets/component-tree/guide-progress-page.svg)

---

## レビュー時に見てほしいこと

1. 画面の情報が自然なまとまりで分かれているか。
2. 共通化すべき部分と画面固有にすべき部分が分かれているか。
3. コア画面の責務が大きくなりすぎていないか。
4. AI相談・AI評価・教材化の領域が画面上で混ざりすぎていないか。
5. 画面データ要求と対応する保存先が説明できる粒度になっているか。
6. 実装時にReactコンポーネントとして切り出しやすい粒度か。
