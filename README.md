# ftp-upload-trial

## GitHub Actionsを使用したFTPアップロード

このリポジトリでは、GitHub Actionsを使用して、ブランチに応じて自動的に適切な環境へファイルをアップロードします：
- `main`ブランチへのプッシュ → 本番環境へデプロイ
- `develop`ブランチへのプッシュ → ステージング環境へデプロイ（有効な場合）

### 特徴
- ブランチに基づく自動デプロイ（main→本番、develop→ステージング）
- 差分のみのアップロード（変更されたファイルのみ転送）
- セキュアな認証情報の管理（GitHub Secrets）
- 環境ごとに分離されたワークフローファイル
- ステージング環境の有効/無効を簡単に切り替え可能
- ソースコード上で管理される共通設定（アップロード対象ディレクトリ、除外パターン）

### ワークフローファイル構成

- `.github/workflows/deploy-staging.yml` - ステージング環境デプロイ用
- `.github/workflows/deploy-production.yml` - 本番環境デプロイ用
- `.github/workflows/config/ftp-config.yml` - FTP共通設定（アップロード対象ディレクトリ、除外パターン）

### セットアップ方法

#### 1. GitHub Secretsの設定

以下のシークレットをリポジトリに設定する必要があります：

1. リポジトリページで「Settings」タブに移動
2. 左側のメニューから「Secrets and variables」→「Actions」を選択
3. 「New repository secret」ボタンをクリックして以下のシークレットを追加：

**本番環境用シークレット：**

| シークレット名 | 説明 | 例 |
|-------------|------|-----|
| `PROD_FTP_SERVER` | 本番FTPサーバーのアドレス | `ftp.example.com` |
| `PROD_FTP_USERNAME` | 本番FTPアカウントのユーザー名 | `username` |
| `PROD_FTP_PASSWORD` | 本番FTPアカウントのパスワード | `password` |
| `PROD_FTP_SERVER_DIR` | 本番環境のアップロード先ディレクトリ | `/public_html` |

**ステージング環境用シークレット（ステージング環境を使用する場合）：**

| シークレット名 | 説明 | 例 |
|-------------|------|-----|
| `STAGING_FTP_SERVER` | ステージングFTPサーバーのアドレス | `ftp.staging.example.com` |
| `STAGING_FTP_USERNAME` | ステージングFTPアカウントのユーザー名 | `staging_user` |
| `STAGING_FTP_PASSWORD` | ステージングFTPアカウントのパスワード | `staging_password` |
| `STAGING_FTP_SERVER_DIR` | ステージング環境のアップロード先ディレクトリ | `/staging` |

#### 2. ステージング環境の有効/無効設定

ステージング環境を有効または無効にするには：

1. リポジトリページで「Settings」タブに移動
2. 左側のメニューから「Secrets and variables」→「Actions」を選択
3. 「Variables」タブをクリック
4. 「New repository variable」ボタンをクリックして以下の変数を追加：
   - 名前: `STAGING_ENABLED`
   - 値: `true`（有効）または `false`（無効）

この変数を設定しない場合、またはfalseに設定した場合、developブランチへのプッシュ時にステージング環境へのデプロイは行われません。

#### 3. FTP設定のカスタマイズ

FTPデプロイの設定をカスタマイズするには、`.github/workflows/config/ftp-config.yml` ファイルを編集します：

- `local_dir`: アップロード対象のローカルディレクトリを指定します（例: `./` または `./dist/`）
- `exclude_patterns`: FTPデプロイ時に除外するファイルやディレクトリのパターンを指定します

このファイルの変更はGitリポジトリで管理され、両方の環境に適用されます。

### 注意事項

- 初回デプロイ時はすべてのファイルがアップロードされます
- 2回目以降は変更されたファイルのみがアップロードされます
- 本番環境とステージング環境で別々の状態ファイルを使用します
  - `.ftp-deploy-sync-state-prod.json` - 本番環境用
  - `.ftp-deploy-sync-state-staging.json` - ステージング環境用
