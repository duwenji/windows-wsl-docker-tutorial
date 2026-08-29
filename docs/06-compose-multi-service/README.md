# 06 Compose Multi Service

## この章で学ぶこと

- `docker-compose.yml`の基本構文とコマンド
- Composeが自動で作るネットワークとサービス発見の仕組み
- Web + DBの実践的な複数サービス構成

## 学習目標

- Composeで複数サービスを連携させて起動できる
- サービス名による名前解決の仕組みを説明できる
- named volumeによるデータ永続化を`docker compose down -v`との違いで確認できる

## 演習案

1. `web`と`db`の2サービス構成を作り、サービス名で通信できることを確認する
2. Redisを1サービス追加し、3サービス構成に拡張する（09章capstoneの準備）

## 成功判定

- `docker-compose.yml`を自力で書ける
- `down`と`down -v`の違いを説明できる

## 次章へ

- [07-operations-and-troubleshooting/README.md](../07-operations-and-troubleshooting/README.md)
