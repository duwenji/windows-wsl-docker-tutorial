# 03 Web + DB構成の実践

## 所要時間

約30分

## 学習目標

- Webアプリ + PostgreSQLの2サービス構成を構築できる
- データ永続化の有無を`-v`オプションの違いで確認できる

---

## 構成

Node.jsの簡易Webアプリと、PostgreSQLデータベースの2サービス構成を作ります。

```bash
mkdir -p ~/projects/web-db-example/web && cd ~/projects/web-db-example
```

## docker-compose.yml

```yaml
services:
  web:
    build: ./web
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgres://app:app@db:5432/app
    depends_on:
      - db
  db:
    image: postgres:16
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: app
      POSTGRES_DB: app
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
```

`web/Dockerfile`:

```dockerfile
FROM node:20-bookworm
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
CMD ["node", "index.js"]
```

`web/index.js`（最小限のHTTPサーバー例）:

```js
const http = require("http");
http.createServer((req, res) => {
  res.writeHead(200, { "Content-Type": "text/plain" });
  res.end("Hello from web-db-example\n");
}).listen(3000, () => console.log("listening on 3000"));
```

`web/package.json`:

```json
{
  "name": "web-db-example",
  "version": "1.0.0",
  "main": "index.js"
}
```

## 起動と動作確認

```bash
docker compose up -d --build
```

Windows側ブラウザで `http://localhost:3000` を開き、応答があることを確認します。

DB接続の確認:

```bash
docker compose exec db psql -U app -d app -c "SELECT 1;"
```

## データ永続化の確認

```bash
# データを1件作成する
docker compose exec db psql -U app -d app -c "CREATE TABLE t(id int); INSERT INTO t VALUES (1);"

# 通常のdown（volumeは残る）
docker compose down
docker compose up -d
docker compose exec db psql -U app -d app -c "SELECT * FROM t;"
# → 1行返る（データが残っている）

# -v付きdown（volumeも削除される）
docker compose down -v
docker compose up -d
docker compose exec db psql -U app -d app -c "SELECT * FROM t;"
# → ERROR: relation "t" does not exist （データが消えている）
```

`docker compose down`だけではnamed volume（`db-data`）は削除されず、`-v`を付けたときだけ削除される、という違いを確認できます。

## 章末チェックリスト

- [ ] Web + DB構成をComposeで起動できた
- [ ] `docker compose down`と`docker compose down -v`の違いを実際に確認できた

## 次へ

- [07-operations-and-troubleshooting/README.md](../07-operations-and-troubleshooting/README.md)
