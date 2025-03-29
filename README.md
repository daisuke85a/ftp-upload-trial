# ftp-upload-trial

## GitHub Actionsを使用したFTPアップロード

このリポジトリでは、GitHub Actionsを使用して、mainブランチへのプッシュ時に自動的にFTPサーバーへファイルをアップロードします。

### 特徴
- mainブランチへのプッシュ時に自動デプロイ
- 差分のみのアップロード（変更されたファイルのみ転送）
- セキュアな認証情報の管理（GitHub Secrets）

### セットアップ方法

#### 1. GitHub Secretsの設定

以下のシークレットをリポジトリに設定する必要があります：

1. リポジトリページで「Settings」タブに移動
2. 左側のメニューから「Secrets and variables」→「Actions」を選択
3. 「New repository secret」ボタンをクリックして以下のシークレットを追加：

| シークレット名 | 説明 | 例 |
|-------------|------|-----|
| `FTP_SERVER` | FTPサーバーのアドレス | `ftp.example.com` |
| `FTP_USERNAME` | FTPアカウントのユーザー名 | `username` |
| `FTP_PASSWORD` | FTPアカウントのパスワード | `password` |
| `FTP_SERVER_DIR` | アップロード先のディレクトリパス | `/public_html` または空白で root |

#### 2. ワークフローファイルの確認

`.github/workflows/ftp-upload.yml` ファイルが既に設定されています。必要に応じて、除外するファイルやディレクトリを `exclude` パラメータで指定できます。

### 注意事項

- 初回デプロイ時はすべてのファイルがアップロードされます
- 2回目以降は変更されたファイルのみがアップロードされます
- `.ftp-deploy-sync-state.json` ファイルは同期状態を管理するために使用されます
