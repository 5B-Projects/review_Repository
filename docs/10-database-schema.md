# 10 データベーススキーマ設計

## 位置づけ

この設計図は、Supabase/PostgreSQLへ実装する約79テーブルの設計方針と索引です。

**DB実装の正本は `supabase/migrations/*.sql`** です。  
このドキュメント群は、命名規約・RLS・migration順序・背景を示す補助資料として扱います。

## 物理名規約の要点

| 規約 | 内容 |
|---|---|
| 命名 | `snake_case` |
| FK列 | `<対象単数形>_id` |
| 技術タグ | `technology_tags` に統一 |
| 状態列 | `_status` |
| 真偽値 | `is_*` / `has_*` / `*_flag` |
| タイムスタンプ | `_at` |
| enum | PostgreSQL enumではなく `text + CHECK` |

## RLS 4パターン

| パターン | 条件例 | 適用先例 |
|---|---|---|
| self | `auth.uid() = user_id` | users, user_profiles, notifications, goal_drafts, carts, guide_progress |
| org | `public.is_org_member(organization_id)` | organizations, organization_members, organization_api_credentials |
| admin | `public.is_admin()` | audit_logs, admin_notes, 管理者コマンド系 |
| service_role | `auth.role() = 'service_role'` | FastAPIワーカー・BFF書込・バッチ |

## マイグレーション概観

| # | ファイル | 責務 |
|---:|---|---|
| 00 | extensions | pgcrypto, uuid-ossp, pg_trgm, vector, pgmq, pg_cron, pg_net, supabase_vault |
| 01 | helpers | set_updated_at(), is_admin(), is_org_member() 等 |
| 02 | users_auth | users, user_profiles |
| 03 | organizations | organizations, organization_members, organization_api_credentials |
| 04 | external_accounts | external_account_links |
| 05 | image_assets | image_assets |
| 06 | technology_tags | technology_tags |
| 07 | legal | legal_documents, legal_consents |
| 08 | notifications_follows | notifications, follows, user_favorite_tags |
| 09 | jobs | jobs |
| 10 | guide_generation | learning_style_defaults, goal_drafts, generation_jobs, generation_steps, ai_executions, guardrail_results, guide_outlines, guides 等 |
| 11 | guide_progress | guide_progress, chapter_progress, step_progress, chat_messages, evaluation_jobs, evaluation_results, guide_completions, material_creation_jobs 等 |
| 12 | materials | materials, material_versions, material_tags, material_sections, material_chapters, material_enrollments, reviews 等 |
| 13 | search_logs | search_query_logs, search_result_snapshots |
| 14 | plans | plans, plan_prices, plan_features, subscriptions, usage_quotas |
| 15 | commerce | carts, cart_items, checkout_sessions, transactions, payment_events, entitlements |
| 16 | history_admin_audit | learning_history_events, reports, form_launch_logs, admin_notes, audit_logs |
| 17 | triggers | updated_at / soft-delete整合 / 物理削除禁止 |
| 18 | indexes | FK, 複合, partial unique, gin(trgm), hnsw(vector) |
| 19 | rls | 全テーブルRLS + 標準4パターンポリシー |
| 20 | storage_buckets | storage.buckets + storage.objects policies |
| 21 | realtime | supabase_realtime publication |
| 22 | cron | TTL cleanup, 孤立jobs再enqueue, usage_quotasリセット, review_summaries再計算 |
| 23 | queues | pgmq キュー作成 |

## レビュー観点

1. `jobs` を非同期処理の共通親にする方針が分かりやすいか。
2. `technology_tags` 統一により、教材・ガイド・検索のタグ参照が混乱しないか。
3. RLSに載せる責務とBFF側で判定する責務が分かれているか。
4. 24マイグレーションの順序が依存関係として自然か。
5. 先生レビューではmigration本文まで見る必要があるか、索引で十分か。
