# 09 Supabase サービス別データ配置方針

## 位置づけ

この設計図は、どのデータを Supabase のどのサービスへ置くかを決める資料です。

対象サービスは主に以下です。

- Postgres
- Supabase Auth
- Storage
- Realtime
- Vault
- pgmq
- pg_cron
- pgvector / pg_trgm

SQL 正本と食い違う場合は、SQLを優先します。

## 主要なデータ配置

| データ種別 | 配置先 | 要点 |
|---|---|---|
| 構造化データ（約79テーブル） | Postgres `public` | 正本。RLS保護。グルーピングは COMMENT + migrations 番号 prefix で表現 |
| 認証 | Supabase Auth | `users.id` は `auth.users.id` と同値だが論理FKのみ。public にトークン/メール/パスワードを持たない |
| プロフィール画像 | Storage `profile-images` | private。`image_assets.storage_path` + Signed URL |
| 教材サムネイル | Storage `material-thumbnails` | public 読み。公開教材の一覧表示に使う |
| 教材本文・Win11版・AI素材 | Storage `material-assets` | private。有料保護・購入者限定 |
| AI入出力本文・プロンプト | Storage `ai-io` | private, TTL。DBには `ai_executions.input_path` / `output_path` を保存 |
| エクスポート成果物 | Storage `exports` | private。短期 Signed URL |
| 環境ファイル資材 | Storage `environment-files` | private。ガイド完了者向け |
| 通知 | Postgres `notifications` + Realtime | 一覧・既読は永続化、配信をRealtimeで補完 |
| 生成進捗 | Postgres `jobs` / `generation_steps` + Realtime | 正本は `jobs`、進捗変化をRealtime push |
| チャットメッセージ | Postgres `chat_messages` + Realtime | 短文ならtext直書き推奨 |
| コード評価ストリーム | `evaluation_jobs.status` + Realtime | 結果ペイロードはStorage参照、Realtimeは進捗のみ |
| 組織API鍵・招待コードsalt | Vault | 平文をDBに置かず、参照とfingerprintを保持 |
| バックグラウンドジョブ | pgmq + `jobs` | `jobs` が状態の正本、pgmqはディスパッチ用 |
| 検索 | Postgres + pg_trgm + pgvector | キーワード検索後に意味リランク。将来差し替え可能 |

## レビュー観点

1. AI本文・コード本文をDBへ直書きしない方針が妥当か。
2. 教材本文を `material-assets` private に置く設計で有料教材を保護できるか。
3. 生成進捗を `jobs` と Realtime で扱う設計が画面要件と合っているか。
4. pgmq + jobs の分担が明確か。
5. 検索をPostgres中心で始め、将来Meilisearch/Typesenseへ差し替えられる設計でよいか。
