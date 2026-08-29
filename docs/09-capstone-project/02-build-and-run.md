# 02 構築と起動

## 所要時間

約40分

## 学習目標

- 3サービス構成を自力で構築・起動できる
- Dev Containersでの開発ループを実践できる

---

## ディレクトリ構成

```
capstone-app/
├── .devcontainer/
│   └── devcontainer.json
├── web/
│   ├── Dockerfile
│   ├── package.json
│   └── index.js
└── docker-compose.yml
```

WSL側で作成します（05章で学んだ通り、必ずWSL側パスに置きます）。

```bash
mkdir -p ~/projects/capstone-app/.devcontainer ~/projects/capstone-app/web
cd ~/projects/capstone-app
```

## docker-compose.yml

06章の構成にRedisサービス（`cache`）を追加した完全な構成です。

```yaml
services:
  web:
    build: ./web
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgres://app:app@db:5432/app
      REDIS_URL: redis://cache:6379
    volumes:
      - ./web:/app
      - node_modules_cache:/app/node_modules
    depends_on:
      - db
      - cache
  db:
    image: postgres:16
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: app
      POSTGRES_DB: app
    volumes:
      - db-data:/var/lib/postgresql/data
  cache:
    image: redis:7

volumes:
  db-data:
  node_modules_cache:
```

`web/Dockerfile`:

```dockerfile
FROM node:20-bookworm
WORKDIR /app
COPY package.json package-lock.json* ./
RUN npm install
COPY . .
CMD ["node", "index.js"]
```

`web/package.json`:

```json
{
  "name": "capstone-app",
  "version": "1.0.0",
  "main": "index.js"
}
```

`web/index.js`:

```js
const http = require("http");
http.createServer((req, res) => {
  res.writeHead(200, { "Content-Type": "text/plain" });
  res.end("Hello from capstone-app (web + db + cache)\n");
}).listen(3000, () => console.log("listening on 3000"));
```

## .devcontainer/devcontainer.json

Composeベースの構成をそのままDev Containersに使います。

```json
{
  "name": "capstone-app",
  "dockerComposeFile": "../docker-compose.yml",
  "service": "web",
  "workspaceFolder": "/app",
  "forwardPorts": [3000]
}
```

- `dockerComposeFile`: 使用する`docker-compose.yml`のパス
- `service`: VS Codeが接続する対象サービス（この場合は`web`）
- `workspaceFolder`: コンテナ内でVS Codeが開くパス

## 起動確認手順

まず単体でComposeが正しく動くか確認します。

```bash
docker compose up -d --build
```

Windows側ブラウザで `http://localhost:3000` を開き、応答を確認します。

Redisへの疎通確認:

```bash
docker compose exec cache redis-cli ping
```

```
PONG
```

DBへの疎通確認:

```bash
docker compose exec db psql -U app -d app -c "SELECT 1;"
```

すべて確認できたら、一度停止し、VS CodeのDev Containersから開き直します。

```bash
docker compose down
```

VS Codeで`~/projects/capstone-app`をRemote-WSL経由で開き、コマンドパレットから`Dev Containers: Reopen in Container`を実行します。`db`・`cache`サービスも含めて自動的に起動し、`web`サービスのコンテナ内で統合ターミナルが開きます。

## 次へ

- [03-final-checklist.md](./03-final-checklist.md)
