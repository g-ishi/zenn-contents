---
title: 'Chrome拡張機能 + Hasura + Firebase で認証機能を実装するための環境構築'
emoji: '🛫'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Chrome拡張機能', 'firebase', 'hasura']
published: true
---

# Chrome 拡張機能 + Hasura + Firebase で認証機能を実装するための環境構築

Chrome 拡張機能 をクライアント、Hasura をバックエンドとして構成しているシステムで、Firebase を使用した認証機能を Chrome Extension に実装します。

以下の記事・ドキュメントを大変参考にさせていただきました。誠にありがとうございます。

- [Chrome App ID 取得](https://zenn.dev/mktu/articles/9f17fe89e74282#chrome-app-id%E5%8F%96%E5%BE%97)
- [Manifest ファイルの記述 (V3)](https://qiita.com/kobenikawaii/items/ae8a0ac2d71d8d6a8bf4#manifest%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%81%AE%E8%A8%98%E8%BF%B0v3)
- [Hasura Authentication Integration with Firebase](https://hasura.io/learn/graphql/hasura-authentication/integrations/firebase/#jwtconfiginhasura)

自分の備忘録として概要を記録しておきます。細かい設定値の記述は省略しているのでご了承ください。

## Firebase プロジェクトの準備

1. Google Cloud コンソールから新規プロジェクトを作成する。
2. Firebase コンソールから、新しい Firebase プロジェクトを上記で作成した既存のプロジェクトに追加する。

※firebase 側からのみプロジェクトを作るとうまくいかないので注意。

## Chrome Extension と Firebase の接続設定

### Firebase 側の設定

1. [Chrome App ID 取得](https://zenn.dev/mktu/articles/9f17fe89e74282#chrome-app-id%E5%8F%96%E5%BE%97)の「準備」のセクションを実施
   1. 「Firebase プロジェクトの設定」のセクションでは、記載されている作業以外に Firebase プロジェクトに Web アプリを追加する。

### アプリ側の設定

1. [Chrome App ID 取得](https://zenn.dev/mktu/articles/9f17fe89e74282#chrome-app-id%E5%8F%96%E5%BE%97)の「Firebase 連携」セクションを実施
   1. `firebaseConfig`には作成した Firebase プロジェクトに作成した Web アプリの認証情報を設定する
   2. manifest.json の文法が V2 になっているので、V3 の文法で記載する。V3 での記載方法は以下の記事を参照
      1. [Manifest ファイルの記述 (V3)](https://qiita.com/kobenikawaii/items/ae8a0ac2d71d8d6a8bf4#manifest%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%81%AE%E8%A8%98%E8%BF%B0v3)

## Hasura / Firebase の連携設定

1. [Hasura Authentication Integration with Firebase](https://hasura.io/learn/graphql/hasura-authentication/integrations/firebase/#jwtconfiginhasura)の「JWT Config in Hasura」セクションを実施

以上で設定は完了です。
