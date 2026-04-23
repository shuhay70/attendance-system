# 勤怠管理システム

ツアー会社のインターン・スタッフ向けに開発した、勤怠・給与管理Webアプリです。

## 概要

スタッフがスマートフォンからワンタップで打刻でき、管理者がリアルタイムで出勤状況を確認・承認できる社内向け業務アプリ。実際の会社での運用を想定して設計・実装しました。

## 機能

### スタッフ側
- 出勤 / 休憩開始 / 休憩終了 / 退勤の打刻
- 打刻忘れ・修正の申請（勤怠修正申請）
- ガイド日当の申請
- 立替経費の請求
- 月次の給与サマリー・申請履歴の確認
- 承認通知のリアルタイム受信

### 管理者側
- 全スタッフのリアルタイム出勤状況
- 月次給与一覧（労働時間・交通費・日当・経費の集計）
- 各種申請の承認 / 差し戻し
- スタッフ情報の管理（時給・往復交通費の設定）
- 新規スタッフの承認フロー

### その他
- 日本語 / 英語の切り替え（localStorage に保存）
- Supabase Row Level Security による権限分離（スタッフ・管理者で別クライアント）
- Vercel へのデプロイ

## 技術スタック

| カテゴリ | 技術 |
|---|---|
| フロントエンド | React 19 + TypeScript |
| スタイリング | Tailwind CSS v4 |
| バックエンド / DB | Supabase (PostgreSQL + Auth + RLS) |
| ビルド | Vite |
| デプロイ | Vercel |

## アーキテクチャの特徴

- **デュアルクライアント設計**: スタッフ用 (`anon key`) と管理者用 (`service role key`) を分離し、RLS と組み合わせて権限制御
- **React Context**: 認証・言語・管理者データを Context で管理し、コンポーネント間の状態を共有
- **楽観的UI不採用・明示的ロード**: 申請・承認は確実性を優先してサーバー応答後にUIを更新

## セットアップ

### 1. Supabase プロジェクト作成

1. [supabase.com](https://supabase.com) でプロジェクトを新規作成
2. `supabase/schema.sql` を Supabase SQL Editor で実行

### 2. 管理者アカウント作成

Supabase 管理画面 → Authentication → Users → 「Add user」でメールアドレスとパスワードを作成後、SQL Editor で実行：

```sql
insert into public.profiles (id, name, role, hourly_wage)
select id, '管理者名', 'admin', 0
from auth.users
where email = 'admin@example.com';
```

### 3. 環境変数の設定

```env
VITE_SUPABASE_URL=https://xxxx.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key
VITE_SUPABASE_SERVICE_ROLE_KEY=your-service-role-key
```

### 4. ローカル開発

```bash
npm install
npm run dev
```

### 5. Vercel デプロイ

1. GitHub にプッシュ
2. Vercel でリポジトリをインポート
3. Environment Variables に上記の環境変数を設定してデプロイ
