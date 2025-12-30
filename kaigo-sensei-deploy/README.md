# 🏥 Kaigo Sensei - Vercelデプロイ手順書

このガイドに従って、Kaigo SenseiをVercelにデプロイしましょう。

---

## 📋 必要なもの

1. ✅ Claude API Key（Anthropicから取得）
2. ✅ Vercelアカウント（無料）
3. ✅ このフォルダの全ファイル

---

## 🔑 ステップ1：Claude APIキーを取得

### 1-1. Anthropicアカウント作成
1. **https://console.anthropic.com** にアクセス
2. 「Sign Up」でアカウント作成
3. メールアドレスを認証

### 1-2. APIキー発行
1. ログイン後、左メニュー「API Keys」をクリック
2. 「Create Key」ボタンを押す
3. キー名を入力（例：「Kaigo Sensei Production」）
4. **生成されたAPIキーをコピー**
   - 形式：`sk-ant-api03-xxxxxxxxxxxxx`
   - ⚠️ 一度しか表示されません！必ず保存

### 1-3. クレジット購入
1. 左メニュー「Settings」→「Billing」
2. 「Add Credit」で$5〜$20をチャージ
3. クレジットカード情報を入力

💡 **コストの目安**: 100ユーザー × 20回/日 × 30日 = 約$150/月

---

## 🚀 ステップ2：Vercelにデプロイ

### 2-1. Vercelアカウント作成
1. **https://vercel.com** にアクセス
2. 「Sign Up」をクリック
3. GitHubアカウントで登録（推奨）

### 2-2. プロジェクトファイル準備

**フォルダ構成を確認：**
```
kaigo-sensei-deploy/
├── api/
│   └── chat.js          ✅ サーバーレス関数
├── public/
│   └── index.html       ✅ V4のHTMLファイル
├── vercel.json          ✅ Vercel設定
└── README.md            ✅ このファイル
```

**public/index.htmlの準備：**
1. V4のHTMLファイルを`public`フォルダに配置
2. ファイル名を`index.html`に変更

### 2-3. デプロイ方法（2つの選択肢）

#### 【方法A：ドラッグ&ドロップ】（簡単）

1. Vercel dashboard で「Add New」→「Project」
2. 「Deploy」タブを選択
3. `kaigo-sensei-deploy`フォルダをドラッグ&ドロップ
4. 「Deploy」ボタンをクリック

#### 【方法B：GitHub経由】（推奨）

1. GitHubで新しいリポジトリ作成
2. ファイルをpush
3. Vercelで「Import Git Repository」
4. リポジトリを選択して「Import」

---

## 🔐 ステップ3：環境変数の設定（重要！）

### 3-1. APIキーを設定

1. デプロイ完了後、プロジェクトページを開く
2. 上部メニュー「Settings」をクリック
3. 左サイドバー「Environment Variables」をクリック
4. 新しい環境変数を追加：

```
Name:  ANTHROPIC_API_KEY
Value: sk-ant-api03-xxxxxxxxxxxxx（あなたのAPIキー）
Environment: Production, Preview, Development（全てチェック）
```

5. 「Save」ボタンをクリック

⚠️ **超重要**: この設定をしないとアプリが動きません！

### 3-2. 再デプロイ

環境変数を設定したら、再デプロイが必要：

1. 上部メニュー「Deployments」をクリック
2. 最新のデプロイの右側「...」（3点メニュー）をクリック
3. 「Redeploy」を選択
4. 「Redeploy」ボタンをクリック

---

## ✅ ステップ4：動作確認

### 4-1. アプリを開く

1. デプロイ完了後、「Visit」ボタンをクリック
2. または `https://your-project-name.vercel.app` にアクセス

### 4-2. 動作テスト

1. 翻訳モードで「Selamat pagi」と入力
2. 日本語が返ってくればOK！✅
3. 練習モード・ロールプレイモードも試す

### 4-3. エラーが出る場合

**「API key not configured」エラー:**
- 環境変数が設定されていません
- ステップ3を再確認

**「Claude API error」:**
- APIキーが間違っている
- クレジットが不足している
- Anthropic consoleで確認

**まっ白な画面:**
- `public/index.html`が配置されていません
- ファイル名を確認

---

## 📁 ファイル構成の詳細

### `/api/chat.js`
- サーバーレス関数
- Claude APIを呼び出す
- APIキーはここで使用（ユーザーには見えない）
- 自動的に `/api/chat` エンドポイントになる

### `/public/index.html`
- V4のフロントエンド
- `/api/chat` を呼び出すように修正必要
- 自動的にルートURL `/` で配信される

### `/vercel.json`
- Vercel設定ファイル
- APIルーティングを定義

---

## 🔄 HTMLファイルの修正

V4のHTMLファイルで、Claude API呼び出し部分を修正：

**変更前：**
```javascript
const response = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
    },
    body: JSON.stringify({...})
});
```

**変更後：**
```javascript
const response = await fetch('/api/chat', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
    },
    body: JSON.stringify({
        prompt: yourPromptHere
    })
});
```

---

## 🎯 本番運用のヒント

### カスタムドメイン設定
1. Vercel「Settings」→「Domains」
2. 独自ドメインを追加
3. DNSレコードを設定

### アクセス解析
1. Vercel「Analytics」タブで確認
2. ユーザー数・リクエスト数を監視

### コスト管理
1. Anthropic console で使用量確認
2. 月次予算アラート設定
3. レート制限の実装を検討

### セキュリティ
- APIキーは**絶対に**GitHubにpushしない
- `.gitignore`に環境変数ファイルを追加
- Vercelの環境変数機能のみ使用

---

## 💰 料金について

### Vercel
- Hobby（無料）プラン：十分使える
- Pro（月$20）: 商用利用、より多くのリソース

### Claude API
- 使った分だけ課金
- Sonnet 4: 約$3 / 100万トークン
- 月間使用量の目安：
  - 10ユーザー: 約$15/月
  - 100ユーザー: 約$150/月
  - 1000ユーザー: 約$1,500/月

---

## 🆘 トラブルシューティング

### デプロイが失敗する
- `vercel.json`の構文エラーを確認
- `api/chat.js`のコードエラーを確認
- Vercelのログを確認（Deployments → ログ）

### APIが応答しない
- 環境変数が正しく設定されているか確認
- 再デプロイを実行
- Vercelの Function Logs を確認

### クレジットが減らない
- リクエストが届いていない
- ブラウザのコンソールでエラー確認
- Network タブでリクエスト確認

---

## 📞 サポート

**Vercel関連:**
- https://vercel.com/docs
- https://vercel.com/support

**Claude API関連:**
- https://docs.anthropic.com
- support@anthropic.com

**Kaigo Sensei関連:**
- プロジェクト管理ファイルを参照
- 開発者（あなた）がサポート

---

## 🎉 完了！

デプロイが成功したら：
1. URLをシェア
2. ユーザーにテストしてもらう
3. フィードバックを収集
4. 改善を継続

**次のステップ:**
- 営業資料作成
- デモ動画作成
- B2B営業開始

頑張ってください！🚀
