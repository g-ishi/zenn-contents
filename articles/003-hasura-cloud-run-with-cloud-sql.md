---
title: 'Hasura/Cloud Run から接続するバックエンド DB の選択肢: Cloud SQL と Neon'
emoji: '🐈'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: [Hasura, CloudRun, CloudSQL, Neon, データベース]
published: true
---

# Hasura/Cloud Run から接続するバックエンド DB の選択肢: Cloud SQL と Neon

Hasura/Cloud Run を利用したアプリケーションのバックエンドデータベースとして、Cloud SQL と Neon のどちらを選択するかについて解説します。それぞれの設定方法と料金について調べて内容をメモとして残します。

詳しい内容はそれぞれ元ドキュメントを参照ください。

## Cloud SQL の場合

Cloud SQL を利用する場合の設定手順については、以下のサイトを参照してください。
[Hasura Cloud Run with Cloud SQL Guide](https://hasura.io/docs/latest/deployment/deployment-guides/google-cloud-run-cloud-sql/#step-8-adding-a-vpc-connector-optional)

### 接続手順

1. Step 7 までの手順を実行すると、Public IP 経由での接続が可能になります。
2. Step 8 の手順を実行すると、サーバレス VPC アクセスを使用した Private IP 経由での接続が可能になります。

### 補足

- Cloud Run はデフォルトではインターネット経由で外部サービスへのアクセスを行います。

## Neon の場合

Neon を利用する場合は、以下の手順で設定を行います。

1. Neon ダッシュボードから Connection String を確認します。
2. "HASURA_GRAPHQL_DATABASE_URL" にその Connection String を設定してデプロイします。

## 料金の比較

Cloud SQL と Neon の料金比較については、以下の記事を参照してください。
[Neon Serverless Postgres Cost Comparison](https://medium.com/@alexeylark/neon-serverless-postgres-cost-comparison-b54786af3f40)

### 概要

- **Neon**:

  - 動的なスケーリングとコスト削減を重視する場合に適しています。
  - 特に、データベースの使用が不定期であったり、低負荷のシナリオに最適です。

- **Cloud SQL**:
  - 安定した高性能が必要で、常に稼働しているデータベースを必要とするシナリオに適しています。
  - 固定費用が高くなる可能性があります。

## まとめ

バックエンドデータベースの選択は、アプリケーションの特性と要求に応じて決定することが重要です。動的なスケーリングとコスト効率を重視する場合は Neon が適していますが、安定したパフォーマンスと高可用性を求める場合は Cloud SQL を選択する方が良いでしょう。
