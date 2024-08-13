---
title: 'Next.js/Github Action/Cloud Runでのプロジェクトセットアップ'
emoji: '🐥'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['nextjs', 'github', 'githubaction', 'googlecloud', 'cloudrun']
published: true
---

# はじめに

このガイドでは、Next.js、Github Actions、Google Cloud Run を使用してプロジェクトをセットアップする手順を紹介します。これにより、アプリケーションの開発からデプロイまでの自動化が実現します。

## 概要

本記事では、以下の手順を説明します。

- Next.js のセットアップ
- Github Actions の設定
- Cloud Run へのデプロイ

# 手順

## 1. Next.js のセットアップ

まず、Next.js プロジェクトを作成します。プロジェクトの作成方法については、[公式ドキュメント](https://nextjs.org/docs)を参照してください。

次に、Docker 環境でのセットアップが必要です。公式の Dockerfile は[こちらのリポジトリ](https://github.com/vercel/next.js/tree/canary/examples/with-docker)にあります。

### 注意点

上記の Dockerfile をそのまま使用すると、Cloud Run へのデプロイ時にエラーが発生ため、以下の行 Dockerfile に追加する必要があります。

```dockerfile
ENV HOSTNAME "0.0.0.0"
```

この変更により、アプリケーションはクラウド環境で正しく動作し、外部からアクセス可能になります。

#### 原因

これは、Next.js バージョン 13.4.13 以降において、アプリケーションがデフォルトで `localhost` にバインドされるように変更されたためです。`localhost` へのバインドはクラウド環境では機能しないため、次の環境変数を Dockerfile に追加して、`0.0.0.0` にバインドする必要があります。

詳細は、参考にした [Issue](https://github.com/vercel/next.js/issues/54133) をご覧ください。

## 2. Google Cloud のセットアップ

次に、Google Cloud 上でのセットアップを行います。

### プロジェクトの作成

Google Cloud コンソールで新しいプロジェクトを作成します。

### Artifact Registry の作成

Artifact Registry を作成し、必要に応じてクリーンアップポリシーを設定します。

### サービスアカウントの作成

Github Actions で使用するサービスアカウントを作成し、Artifact Registry にプッシュする権限を付与します。さらに、以下の記事を参考に追加の権限を設定します。

[参考記事](https://zenn.dev/otot_dev/articles/21aed1c3fda911)

## 3. Github Actions の設定

最後に、CI/CD パイプラインを設定します。以下の Github Actions 設定ファイルをプロジェクトに追加します。

```yaml
name: CI/CD pipeline

on:
  push:
    branches:
      - main

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v3

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - id: 'auth'
        uses: 'google-github-actions/auth@v2'
        with:
          credentials_json: '${{ secrets.GCLOUD_SERVICE_ACCOUNT_JSON }}'

      - name: 'Set up Cloud SDK'
        uses: 'google-github-actions/setup-gcloud@v2'

      - name: Set Google Cloud Project
        run: gcloud config set project <project-id>
        env:
          GOOGLE_APPLICATION_CREDENTIALS: ${{ secrets.GCLOUD_SERVICE_ACCOUNT_JSON }}

      - name: Log in to Google Artifact Registry
        run: gcloud auth configure-docker <your-region>-docker.pkg.dev
        env:
          GOOGLE_APPLICATION_CREDENTIALS: ${{ secrets.GCLOUD_SERVICE_ACCOUNT_JSON }}

      - name: Deploy to Cloud Run
        run: |
          gcloud run deploy <service-name> \
          --image <image-name> \
          --region <region>
        env:
          GOOGLE_APPLICATION_CREDENTIALS: ${{ secrets.GCLOUD_SERVICE_ACCOUNT_JSON }}
```

### Secrets の設定

`GCLOUD_SERVICE_ACCOUNT_JSON` には、手順 2 で作成したサービスアカウントの JSON キーを Github リポジトリの Secrets に登録します。

以上で、Next.js を Google Cloud Run にデプロイするためのセットアップが完了です。
