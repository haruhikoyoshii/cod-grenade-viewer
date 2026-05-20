# CoD Grenade Viewer

CoD（Search & Destroy）向けに、グレネード・壁抜き・スナイパーラインのポジション情報を検索・閲覧できるWebアプリです。

## Demo

> デプロイ後にURLを追加予定

## Features

- マップ / サイド / サイト / タイプ / シチュエーションによる絞り込み検索
- 各ポジションを3ステップ（立ち位置 → 視点合わせ → 着弾位置）で画像付きで表示
- 管理者用の投稿ページ（画像アップロード + DB登録）

## Tech Stack

| 役割 | 技術 |
|------|------|
| Frontend | HTML / JavaScript（フレームワークなし） |
| Database | Supabase (PostgreSQL) |
| Storage | Supabase Storage |
| Auth | Supabase Auth |
| Hosting | Vercel（予定） |

## Architecture

```
Browser → Vercel (Static Hosting) → Supabase REST API → PostgreSQL + Storage
```

APIサーバを持たず、SupabaseのREST APIをフロントから直接呼ぶ構成です。動画はYouTube埋め込みで帯域コストを回避しています。

## Database Schema

**maps** — マップ情報  
**positions** — ポジション情報（side / site / type / situation + 3ステップの画像・説明）

## Getting Started

1. `index.html` をブラウザで開くだけで動作します（静的ファイルのみ）
2. Supabase の接続情報はコード内に記載済み（publishable key のみ使用）

## Roadmap

- [ ] Vercel デプロイ
- [ ] 投稿機能（Auth + RLS）
- [ ] お気に入り機能
- [ ] 英語対応
- [ ] React 化
