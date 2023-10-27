---
title: 'Mac(M1)でOracleDBコンテナをビルドする'
emoji: '🐕'
type: 'tech'
topics: ['oracle', 'docker', 'container', 'mac', 'm1']
published: false
---

# はじめに

## 概要

本記事では Mac(M1)で Oracle のデータベースコンテナをビルドして、起動する方法を記載します。

## 背景

Oracle のデータベースをコンテナで動かす機会があり、試行錯誤したので手順を残しておきます。

# 動作環境

以下の環境で動作確認をしています。

- Mac

  - OS: Sonoma 14.0
  - CPU: M1
  - メモリ: 16GB

- コンテナのランタイム

  - podman version 4.6.2
  - Docker version 24.0.6

  コンテナのランタイムは podman を使用していて、コマンドは docker を使っています。

# 手順

1. リポジトリのクローン

任意のディレクトリに[こちらのリポジトリ](https://github.com/oracle/docker-images/tree/main)をクローンします。

```sh
$ git clone https://github.com/oracle/docker-images.git
```

2. 対象のディレクトリに移動

今回は SingleInstance の OracleDB イメージをビルドします。

```sh
$ cd docker-images/OracleDatabase/SingleInstance/dockerfiles
```

3. `Oracle Database 19c Enterprise Edition`のバイナリーをダウンロード

> Linux ARM64 Support: Oracle Database 19c Enterprise Edition is now supported on ARM64 platforms. You will have to provide the installation binaries of Oracle Database 19c and put them into the dockerfiles/19.3.0 folder. The needed file is named LINUX.ARM64_1919000_db_home.zip.

[こちらの README](https://github.com/oracle/docker-images/tree/main/OracleDatabase/SingleInstance)に記載されている通り、ARM64 での実行は`Oracle Database 19c Enterprise Edition`のみがサポートされているので、今回はこちらのバージョンを使用します。

![ダウンロードしたバイナリー](/images/001-oracle-container/image-1.png)

3-1. [こちらのサイト](https://www.oracle.com/database/technologies/oracle19c-linux-arm64-downloads.html)からバイナリーをダウンロードします。(Oracle のサイトでのユーザ登録が必要です。)

3-2. ダウンロードしたファイルは`LINUX.ARM64_1919000_db_home.zip`という名前で`docker-images/OracleDatabase/SingleInstance/dockerfiles/19.3.0`ディレクトリに配置します。

1. コンテナイメージのビルドを行います。

あらかじめ用意されているビルド用のスクリプトを使用してビルドを行います。

```sh
$ pwd
<任意のディレクトリ>/docker-images/OracleDatabase/SingleInstance/dockerfiles

# イメージのビルド
$ ./buildContainerImage.sh -v 19.3.0 -t oracle-db:19.3.0 -x
```

-t はイメージタグ、-v はバージョン、-x は`Enterprise Edition`を表しています。

他のオプションやより詳しい説明は、`./buildContainerImage.sh -h`で確認できます。

5. ビルドしたイメージの起動

先ほどビルドしたイメージからコンテナを起動します。

```sh
docker run --name oracle-19-db \
  -p 1521:1521 -p 5500:5500 -p 2484:2484 \
  --ulimit nofile=1024:65536 --ulimit nproc=2047:16384 --ulimit stack=10485760:33554432 --ulimit memlock=3221225472 \
  -e ORACLE_PWD=password1234 \
  oracle-db:19.3.0
```

コンテナイメージの起動オプションや、各オプションの説明は[こちら](https://github.com/oracle/docker-images/tree/main/OracleDatabase/SingleInstance#running-oracle-database-enterprise-and-standard-edition-2-in-a-container)を参照ください。

使用するバージョンによって起動オプションが若干違っているようなので、ご注意ください。

以下のようなログがコンテナログに出力されていれば OK です。

```txt
#########################
DATABASE IS READY TO USE!
#########################
```

6. データベースに接続できることを確認(任意)

以下のようなコマンドでデータベースに接続できることを確認します。

```sh
docker exec -it oracle-db sqlplus sys/<your password>@//localhost:1521/<your service name> as sysdba
docker exec -it oracle-db sqlplus system/<your password>@//localhost:1521/<your service name>
docker exec -it oracle-db sqlplus pdbadmin/<your password>@//localhost:1521/<Your PDB name>
```

# まとめ

- 本記事では Mac(M1)で OracleDB コンテナをビルドする手順を記載しました。

# 参考文献/リンク

- docker-images リポジトリ

  - https://github.com/oracle/docker-images/tree/main

- 今回参考にしたリポジトリ内の README

  - こちらの REAMDE に記載の手順を参考にしています。
  - https://github.com/oracle/docker-images/tree/main/OracleDatabase/SingleInstance
