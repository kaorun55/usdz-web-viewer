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

**注意**: 多くの脆弱性警告が表示されますが、開発環境では動作します。

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

正常に起動すると、以下のようなメッセージが表示されます：

```
App running at:
- Local:   http://localhost:8082/
- Network: http://[your-ip]:8082/
```

## アクセス方法
ブラウザで http://localhost:8082/ を開き、USDZファイルをアップロードして3Dモデルを表示できます。

## トラブルシューティング

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

## 注意事項
- TypeScript型定義の非互換性により本番ビルド(`npm run build`)は型エラーで失敗する可能性がありますが、開発サーバーは正常に動作します
- Node.js v24などの新しいバージョンでも、OpenSSLレガシープロバイダーの設定により動作します
- 多くのパッケージが非推奨となっていますが、現時点では動作に影響はありません

## 開発サーバーの停止
ターミナルで `Ctrl + C` を押すことで開発サーバーを停止できます。