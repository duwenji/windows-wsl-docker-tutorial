# 03 Secret管理の基本

## 所要時間

約15分

## 学習目標

- 環境変数に認証情報を直接書くリスクを説明できる
- `.env`ファイルを使った基本的な管理ができる
- 実務での推奨アプローチを理解する

---

## environment変数に直接書かない理由

`docker-compose.yml`に直接パスワードなどを書き込むと、以下のようなリスクがあります。

```yaml
# 避けるべき例
environment:
  POSTGRES_PASSWORD: my-secret-password
```

- `docker inspect <container>`で環境変数の値が平文のまま見える
- `docker history <image>`でビルド時の値が残っている場合がある
- Gitにコミットしてしまうと、履歴からも消せなくなる

## .envファイルとComposeでの扱い

Docker Composeは、同じディレクトリに`.env`ファイルがあれば自動的に読み込みます。

`.env`:

```
POSTGRES_PASSWORD=my-secret-password
```

`docker-compose.yml`:

```yaml
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
```

⚠️ `.env`ファイルは**必ず`.gitignore`に追加**し、リポジトリにコミットしないようにしてください。

```
# .gitignore
.env
```

## Docker Secrets（Swarm機能）に軽く触れる

Docker EngineにはSwarmモード専用の`docker secret`という仕組みもあり、より安全にシークレットをコンテナへ渡すことができます。本教材ではSwarmモード自体を扱わないため深入りしませんが、単一ホストでの運用を超えてクラスタ運用を検討する段階になったら選択肢に入る、という位置づけだけ覚えておいてください。

## 実務での推奨

実務では、`.env`ファイルによる管理はローカル開発環境に留め、本番環境やCI/CD環境では以下のような仕組みを使い、**リポジトリには一切平文のシークレットを置かない**ことが推奨されます。

- クラウドプロバイダのシークレットマネージャ
- CI/CDプラットフォームのシークレット機能（暗号化された環境変数など）

## 演習

1. `.env`ファイルと`docker-compose.yml`の`${VAR}`構文を使って、パスワードを環境変数化する
2. `.gitignore`に`.env`を追加し、`git status`で追跡対象外になっていることを確認する

## 章末チェックリスト

- [ ] 環境変数に直接シークレットを書くリスクを説明できる
- [ ] `.env`ファイルと`.gitignore`の組み合わせを実践できた
- [ ] 実務ではリポジトリに平文シークレットを置かない方針を理解している

## 次へ

- [04-docker-desktop-licensing-notes.md](./04-docker-desktop-licensing-notes.md)
