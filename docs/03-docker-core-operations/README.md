# 03 Docker Core Operations

## この章で学ぶこと

- イメージの取得・ビルド・削除
- コンテナの起動・停止・削除・ログ確認
- named volumeとbind mountの基本
- Dockerの既定ネットワークとコンテナ間通信の最小構成

## 学習目標

- 基本的な`docker`コマンドをWSL上で一通り実行できる
- データ永続化のためにvolumeを使い分けられる
- ユーザー定義bridgeでコンテナ間通信ができる

## 演習案

1. `Dockerfile`から自作イメージをビルドし、コンテナとして起動する
2. named volumeでデータを永続化する
3. 2つのコンテナをユーザー定義bridgeに接続し、コンテナ名で通信できることを確認する

## 成功判定

- 基本コマンド（`run`, `ps`, `logs`, `exec`, `stop`, `rm`）を迷わず使える
- コンテナ削除でデータが消える理由と、それを防ぐ方法を説明できる

## 次章へ

- [04-networking-deep-dive/README.md](../04-networking-deep-dive/README.md)
