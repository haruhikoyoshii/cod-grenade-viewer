# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

CoD（S&D）向けに、グレポジ・壁抜き・スナイパー情報を検索・閲覧できる静的Webアプリ。  
世界公開＋ポートフォリオ兼用。APIサーバは持たず、SupabaseのREST APIを直接呼ぶ構成。

## アーキテクチャ

```
Browser → Vercel (静的ホスティング) → Supabase REST API → PostgreSQL + Storage
```

- フロント: 素のHTML + JavaScript（フレームワークなし）
- BaaS: Supabase（PostgreSQL / REST API / Storage / Auth）
- 動画: YouTube埋め込み（帯域コスト回避）

## Supabase

- Project URL: `https://tcfkrbtartmwlxtoxfmy.supabase.co`
- フロントには publishable key のみ使用。`service_role` key はフロントに置かない
- RLS 有効化済み。`maps` / `positions` ともに SELECT の public policy 設定済み（書き込み不可）

## DBスキーマ

### `maps`
| 列 | 型 |
|---|---|
| id | uuid (PK) |
| name | text |
| game_version | text |

### `positions`
| 列 | 型 | 備考 |
|---|---|---|
| id | uuid (PK) | |
| map_id | uuid | maps(id) 参照 |
| side | text | attack / defense |
| site | text | A / B |
| type | text | grenade / wallbang / snipe |
| situation | text | retake / entry / hold |
| throw_from | text | 旧列（非推奨） |
| land_to | text | 旧列（非推奨） |
| image_path | text | 旧列（非推奨） |
| video_url | text | |
| description | text | |
| throw_pos | text | ① 立ち位置（説明） |
| aim_pos | text | ② 視点合わせ（説明） |
| land_pos | text | ③ 着弾位置（説明） |
| throw_img | text | ① 立ち位置（画像URL） |
| aim_img | text | ② 視点合わせ（画像URL） |
| land_img | text | ③ 着弾位置（画像URL） |

## 現在の実装状態

- side / site ドロップダウン + 検索 → Supabase 取得 → カード表示まで動作確認済み
- カードは3ステップ表示（①立ち位置 / ②視点合わせ / ③着弾位置）
- map / type / situation フィルタは追加予定

## 今後の拡張方針

投稿機能（Auth + RLS拡張）→ お気に入り → 英語対応 → React化
