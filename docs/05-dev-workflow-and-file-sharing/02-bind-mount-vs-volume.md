# 02 bind mount vs volumeの性能

## 所要時間

約15分

## 学習目標

- bind mountの性能がマウント元の場所に依存することを説明できる
- named volumeが常に高速である理由を説明できる
- 開発時の推奨パターンを実践できる

---

## bind mountの性能はどこに置くかで決まる

前のレッスンで確認した通り、WSL側ファイルシステムとWindows側ファイルシステムの間には明確な性能差があります。これは03章で扱ったbind mountにもそのまま影響します。

- bind mount元がWSL側パス（`~/projects/...`）→ コンテナ内からのI/Oも高速
- bind mount元がWindows側パス（`/mnt/c/Users/.../projects`）→ 9p経由のオーバーヘッドがそのままコンテナ内I/Oにも乗り、大幅に遅くなる

```bash
# 高速: WSL側パスをbind mount
docker run -v ~/projects/sample-app:/app ubuntu ls /app

# 低速: Windows側パスをbind mount
docker run -v /mnt/c/Users/<ユーザー名>/projects/sample-app:/app ubuntu ls /app
```

⚠️ この違いは見た目上は同じ`-v`オプションの使い方なので気づきにくく、「Dockerが遅い」という誤解の典型的な原因になります。実際に遅いのはDockerではなく、ファイルシステム境界です。

## named volumeが常に高速な理由

03章で見た通り、named volumeはDocker Engine自身が管理する領域で、その実体は常にWSL2 VM内（つまりLinuxネイティブなファイルシステム）に存在します。ホスト側のどこにマウント元を置くかを気にする必要がないため、**named volumeは常に高速**です。

## 開発時の推奨パターン

| 用途 | 推奨する方法 |
|---|---|
| ソースコード（頻繁に編集する） | WSL側パスにcloneし、bind mount |
| データベースのデータなど永続化データ | named volume |
| ビルド成果物のキャッシュ（`node_modules`など） | named volumeでコンテナ内に閉じ込め、bind mountの対象から外す |

3つ目のパターンは特に効果的です。ソースコード全体をbind mountしつつ、`node_modules`だけは別途named volumeにすることで、ホスト側との同期が不要な依存パッケージ部分の速度低下を避けられます。

```yaml
# docker-compose.yml の例（06章で詳しく扱う）
services:
  web:
    build: .
    volumes:
      - ./:/app
      - node_modules_cache:/app/node_modules

volumes:
  node_modules_cache:
```

## 演習

1. WSL側パスとWindows側パスの両方でbind mountしたコンテナを起動し、コンテナ内から`time ls -la`などで体感速度を比較する
2. 上記の`node_modules_cache`パターンを試し、`node_modules`だけがnamed volumeに切り出されていることを`docker volume ls`で確認する

## 章末チェックリスト

- [ ] bind mountの速度がマウント元の場所に依存することを説明できる
- [ ] named volumeが常に高速である理由を説明できる
- [ ] ソースコード・永続化データ・依存パッケージキャッシュの使い分けができる

## 次へ

- [03-vscode-dev-containers.md](./03-vscode-dev-containers.md)
