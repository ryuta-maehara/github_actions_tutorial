# github_actions_tutorial

[![backend](https://github.com/ryuta-maehara/github_actions_tutorial/actions/workflows/backend.yml/badge.svg)](https://github.com/ryuta-maehara/github_actions_tutorial/actions/workflows/backend.yml)

## ローカルビルド

このリポジトリでは `backend` の Express アプリを Cloud Run で動かすための `Dockerfile` を用意しています。

### 前提

- Docker がインストールされていること
- Docker デーモンに接続できること（`docker run hello-world` などで確認）

### ローカルイメージのビルド

```bash
cd ~/document/github_actions_tutorial
sudo docker build -f backend/Dockerfile -t local-backend .
```

> `permission denied` が出る場合は、`sudo` で実行するか、`docker` グループにユーザーを追加した後に再ログインしてください。

### ローカルイメージの起動

```bash
sudo docker run --rm -p 8080:8080 local-backend
```

起動後、ブラウザまたは `curl` で `http://localhost:8080/api/health` を確認できます。

## GitHub Actions CI/CD

このリポジトリでは GitHub Actions を使ってバックエンドのテストと Cloud Run へのデプロイを行う方針です。Cloud Build は今回の学習では使いません。

### 使い方
GitHub Actions は `main` ブランチへの push で次を実行します。

- `backend` の依存関係インストール
- ユニットテスト実行
- バックエンドのビルド
- Docker イメージのビルド
- Artifact Registry へ push
- Cloud Run へデプロイ

### GitHub Secrets
次のシークレットを GitHub リポジトリに設定してください。

- `GCP_PROJECT_ID`: GCP プロジェクト ID
- `GCP_SA_KEY`: Cloud Run と Artifact Registry にアクセスできるサービスアカウントの JSON キー

### Artifact Registry の準備
Artifact Registry の Docker リポジトリを作成しておく必要があります。例:

```bash
gcloud artifacts repositories create github-actions-backend-repo \
  --repository-format=docker \
  --location=asia-northeast1
```

`IMAGE_NAME` は次の形式になります。

```
asia-northeast1-docker.pkg.dev/${{ secrets.GCP_PROJECT_ID }}/github-actions-backend-repo/github_actions_tutorial-backend
```

### ローカルビルド

```bash
cd ~/document/github_actions_tutorial
sudo docker build -f backend/Dockerfile -t local-backend .
```

> `permission denied` が出る場合は、`sudo` で実行するか、`docker` グループにユーザーを追加した後に再ログインしてください。

### ローカルイメージの起動

```bash
sudo docker run --rm -p 8080:8080 local-backend
```

起動後、ブラウザまたは `curl` で `http://localhost:8080/api/health` を確認できます。
