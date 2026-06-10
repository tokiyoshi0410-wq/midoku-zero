# 未読ゼロ (働き名)

スマホのブラウザだけで Gmail の未読を一括既読化する静的ウェブアプリ。
バックエンドなし — メールデータ・トークンは一切サーバーを経由しない
(= Google の CASA セキュリティ監査が免除され、無料の通常 OAuth 審査で一般公開できる)。

設計: [DESIGN.md](DESIGN.md) / 実装計画: [PLAN.md](PLAN.md)

## 構成

| ファイル | 役割 |
|---|---|
| `index.html` | 本体 (vanilla JS、GIS token client + Gmail API 直 fetch) |
| `privacy.html` | プライバシーポリシー (Limited Use 準拠を明記、審査要件) |
| `terms.html` | 利用規約 |

## ローカル起動

GIS は `file://` では動かないので必ず HTTP で配信する:

```
python -m http.server 8000 --directory C:/Users/yusuke/midoku-zero
```

→ http://localhost:8000

## GCP 設定 (初回のみ)

1. console.cloud.google.com で新規プロジェクト (例: midoku-zero)
2. 「API とサービス」→ Gmail API を有効化
3. OAuth 同意画面: External / テストモード、test users に自分の Gmail を追加、
   scope に `gmail.modify` を追加
4. 認証情報 → OAuth クライアント ID (ウェブアプリケーション) を作成
   - 承認済み JavaScript 生成元: `http://localhost:8000` (+ 公開時は Pages の origin)
   - リダイレクト URI は不要 (GIS token flow)
5. 発行されたクライアント ID を `index.html` 冒頭の `CONFIG.clientId` に設定

## 公開 (ユーザー判断後)

1. GitHub repo (tokiyoshi0410-wq/midoku-zero) + GitHub Pages
2. OAuth クライアントの承認済み生成元に `https://tokiyoshi0410-wq.github.io` を追加
3. テストモードのまま友達 (最大 100 人) を test users に追加して需要検証
4. 手応えがあれば restricted scope 審査 (デモ動画 + ポリシー提出、数週間) → 一般公開

## 既知の罠

- Gmail `messages.list` は 0 件のとき空 body を返す → `|| {}` ガード済み
- テストモード中は test users に登録した人しかログインできない
- OAuth ブランド名 = サービス名。「未読ゼロ」は働き名なので公開前に確定する
