# ツールボックス

Vue3で作成された便利ツール集です。

## セットアップ

```bash
npm install
```

## 開発サーバーの起動

```bash
npm run dev
```

## ビルド

```bash
npm run build
```

## GitHub Pagesへのデプロイ

### 初回設定

1. GitHubリポジトリの **Settings** > **Pages** に移動
2. **Source** で **GitHub Actions** を選択して保存
3. **Settings** > **Actions** > **General** に移動
4. **Workflow permissions** セクションで **Read and write permissions** を選択して保存

### 自動デプロイ

設定が完了すると、`main`ブランチにプッシュするたびに自動的にGitHub Pagesにデプロイされます。

デプロイの状態は **Actions** タブで確認できます。

### 手動デプロイ（オプション）

```bash
npm run build
# distフォルダの内容をgh-pagesブランチにプッシュ
```
