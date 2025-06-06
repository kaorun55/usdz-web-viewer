# usdz-web-viewer インストール手順（更新版）

## 前提条件
- Node.js（v14以降推奨）
- npm

## インストール手順

### 1. プロジェクトディレクトリに移動
```bash
cd /path/to/usdz-web-viewer2
```

### 2. 依存関係のインストール
```bash
npm install
```

**注意**: 以下のような警告が表示されますが、開発環境では正常に動作します：
- 脆弱性警告（96-170件程度）
- 非推奨パッケージ警告（Vue 2 EOL、rimraf、eslintなど）
- Node.jsエンジン互換性警告（@achrinza/node-ipc）

これらの警告は無視して進めてください。

### 3. Node.js互換性の修正
Node.js v17以降でOpenSSLエラーが発生するため、`package.json`のスクリプトを修正する必要があります。

`package.json`を編集し、以下のように`scripts`セクションを変更：

```json
"scripts": {
  "serve": "NODE_OPTIONS=--openssl-legacy-provider vue-cli-service serve",
  "build": "NODE_OPTIONS=--openssl-legacy-provider vue-cli-service build",
  "lint": "vue-cli-service lint",
  "deploy": "npm run copydeps & npm run build & firebase deploy",
  "copydeps": "copyfiles -u 3 \"node_modules/three-usdz-loader/external/**/*\" public/wasm"
}
```

### 4. WebAssembly依存関係のコピー
```bash
npm run copydeps
```

このコマンドにより、three-usdz-loaderのWebAssembly関連ファイルが`public/wasm`ディレクトリにコピーされます。

### 5. 開発サーバーの起動
```bash
npm run serve
```

**所要時間**: 初回ビルドは2-3分程度かかります。

正常に起動すると、以下のようなメッセージが表示されます：

```
DONE  Compiled successfully in 2563ms

App running at:
- Local:   http://localhost:8082/
- Network: http://[your-ip]:8082/

No issues found.
```

## アクセス方法
ブラウザで http://localhost:8082/ を開き、USDZファイルをアップロードして3Dモデルを表示できます。

## トラブルシューティング

### クリーンインストールが必要な場合
問題が発生した場合は、以下でクリーンインストール：
```bash
rm -rf node_modules package-lock.json
npm install
npm run copydeps
npm run serve
```

### browserslist警告が表示される場合
以下のコマンドを実行してください：
```bash
npx update-browserslist-db@latest
```

### ポート8082が使用中の場合
別のポートを指定して起動：
```bash
PORT=8080 npm run serve
```

### ビルドが遅い場合
初回ビルドは特に時間がかかります。2回目以降は高速化されます。

## 注意事項
- TypeScript型定義の非互換性により本番ビルド(`npm run build`)は型エラーで失敗する可能性がありますが、開発サーバーは正常に動作します
- Node.js v24.1.0で動作確認済み（OpenSSLレガシープロバイダーの設定により）
- 多くのパッケージが非推奨となっていますが、現時点では動作に影響はありません
- `package.json`にはすでにOpenSSLレガシープロバイダーの設定が含まれています

## 開発サーバーの停止
ターミナルで `Ctrl + C` を押すことで開発サーバーを停止できます。