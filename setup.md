CoD Grenade / Wallbang Viewer

**開発メモ（Supabase構成・MVP）**

## 0. 概要

- 目的：CoD（S&D）向けの**グレポジ / 壁抜き / スナイパーライン**を検索・閲覧できるサイト
- 方針：**閲覧専用（初期）**、無料枠で世界公開、後から拡張
- 構成：フロント（静的） + BaaS（Supabase）

---

## 1. アーキテクチャ

```
[Browser]
   ↓
[Vercel (Static Hosting)]
   ↓
[Supabase REST API]
   ↓
[PostgreSQL] + [Storage]
```

- APIサーバは持たない（初期）
- 動画はYouTube埋め込み（帯域コスト回避）

---

## 2. 技術スタック

- Frontend: HTML + JavaScript（後でReact化可能）
- Backend: Supabase（PostgreSQL / REST API / Storage / Auth）
- Hosting: Vercel
- Video: YouTube（unlisted）

---

## 3. Supabase セットアップ

### 3.1 プロジェクト作成

- Project name: `cod-grenade-map`
- Region: Tokyo
- DB Password: **各自で安全に管理（リポジトリに記載しない）**

---

### 3.2 拡張機能（初回のみ）

```
create extensionifnotexists"pgcrypto";
```

---

## 4. データベース設計

### 4.1 `maps`

```
createtable maps (
  id uuidprimarykeydefault gen_random_uuid(),
  name textnotnull,
  game_version textnotnull
);
```

### 4.2 `positions`

```
createtable positions (
  id uuidprimarykeydefault gen_random_uuid(),
  map_id uuidreferences maps(id),
  side textnotnull,-- attack / defense
  site textnotnull,-- A / B
  type textnotnull,-- grenade / wallbang / snipe
  situation textnotnull,-- retake / entry / hold
  throw_from text,
  land_to text,
  image_path text,
  video_url text,
  description text
);
```

---

## 5. Row Level Security（公開設定）

### 5.1 有効化

```
altertable maps enablerowlevel security;
altertable positions enablerowlevel security;
```

### 5.2 読み取り許可（全体公開）

```
create policy"public read maps"
on maps
forselect
using (true);

create policy"public read positions"
on positions
forselect
using (true);
```

---

## 6. Storage（画像管理）

### 6.1 バケット作成

- Bucket名: `grenades`
- Public: ON

### 6.2 パス設計例

```
grenades/
  └ map_name/
      └ situation/
          └ image.png
```

### 6.3 URL取得

フロントから公開URLを使用する。

---

## 7. 初期データ投入

```
insertinto maps (name, game_version)
values ('Terminal','MW3');

insertinto positions (
  map_id, side, site, type, situation,
  throw_from, land_to, description
)
select
  id,'attack','A','grenade','retake',
'A long','Default','Basic retake nade'
from maps;
```

---

## 8. フロント接続

### 8.1 環境変数（例）

```
SUPABASE_URL=xxx
SUPABASE_ANON_KEY=xxx
```

※ `.env` はコミットしない

※ `.env.example` を用意する

---

### 8.2 接続コード（最小）

```
<scriptsrc="https://cdn.jsdelivr.net/npm/@supabase/supabase-js"></script>
<script>
constsupabase=supabase.createClient(
import.meta.env.VITE_SUPABASE_URL,
import.meta.env.VITE_SUPABASE_ANON_KEY
);

asyncfunctionloadPositions() {
const { data, error }=awaitsupabase
.from("positions")
.select("*");

console.log(data);
}
</script>
```

---

## 9. MVP機能

- マップ選択
- 攻撃 / 守備
- A / B
- シチュエーション
- 一覧表示（画像 / 動画 / 説明）

---

## 10. 非機能要件

- 無料枠で運用
- 世界公開（CDN）
- モバイル対応
- 高速表示（静的配信）

---

## 11. セキュリティ方針

- anon key は公開前提
- 書き込みは許可しない（初期）
- 機密情報は `.env` のみ

---

## 12. 今後の拡張

- 投稿機能（Auth + RLS拡張）
- お気に入り
- 英語対応
- React化
- API層追加（Flask）

---

## 13. 技術選定理由（要約）

- Supabase：API不要で高速開発
- YouTube：帯域コスト回避
- Vercel：グローバルCDN
- MVP重視：まず公開・検証

---

## 14. 開発手順（簡略）

1. Supabaseプロジェクト作成
2. テーブル作成
3. RLS設定
4. ダミーデータ投入
5. フロント接続確認
6. UI実装
7. Vercelデプロイ