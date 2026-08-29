# 01 Docker Composeの基本

## 所要時間

約15分

## 学習目標

- `docker-compose.yml`の最小構成を書ける
- Composeの基本コマンドを使える

---

## docker-compose.ymlの最小構成

```bash
mkdir -p ~/projects/compose-basics && cd ~/projects/compose-basics
```

`docker-compose.yml`:

```yaml
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
```

03章で`docker run -p 8080:80 nginx`と書いていたのと同じ内容を、宣言的なYAMLファイルとして定義したものです。

## 基本コマンド

```bash
# 起動（バックグラウンド）
docker compose up -d

# サービスの状態確認
docker compose ps

# ログ確認
docker compose logs -f

# 停止して削除
docker compose down
```

`docker compose up -d`一発で、`docker-compose.yml`に書かれた全サービスの起動、ネットワークの作成までがまとめて行われます。

## Composeプラグインの確認

02章で`docker-compose-plugin`をインストール済みのため、ハイフンなしの`docker compose`サブコマンドが使えます。

```bash
docker compose version
```

```
Docker Compose version v2.x.x
```

⚠️ 古い情報では`docker-compose`（ハイフンあり、別コマンド）という表記をよく見かけますが、これは旧バージョンのスタンドアロンツールです。本教材では現行の`docker compose`（プラグイン形式）を使います。

## 演習

1. `docker-compose.yml`を作成し、`docker compose up -d`で起動する
2. Windows側ブラウザから`http://localhost:8080`にアクセスできることを確認する
3. `docker compose down`で停止・削除する

## 章末チェックリスト

- [ ] `docker-compose.yml`の最小構成を書けた
- [ ] `up` / `ps` / `logs` / `down`の基本コマンドを使えた

## 次へ

- [02-service-discovery-and-network.md](./02-service-discovery-and-network.md)
