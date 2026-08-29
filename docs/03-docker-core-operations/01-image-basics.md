# 01 イメージの基本操作

## 所要時間

約20分

## 学習目標

- イメージの取得・一覧・削除ができる
- 簡単なDockerfileからイメージをビルドできる
- レイヤキャッシュの考え方を理解する

---

## イメージの取得

```bash
docker pull nginx:latest
```

タグを省略すると自動的に`latest`が使われます。

```bash
docker pull nginx
```

## イメージ一覧と削除

```bash
# イメージ一覧
docker images
```

```
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
nginx        latest    605c77e624dd   2 weeks ago    142MB
```

```bash
# イメージの削除
docker rmi nginx:latest

# 使われていないイメージを一括削除
docker image prune
```

## Dockerfileからのビルド

最小構成の確認用アプリを作ります。

```bash
mkdir -p ~/projects/sample-app && cd ~/projects/sample-app
```

`index.html`:

```html
<h1>Hello from Docker on WSL2</h1>
```

`Dockerfile`:

```dockerfile
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html
```

ビルドコマンド:

```bash
docker build -t sample-app:1.0 .
```

- `-t sample-app:1.0`: イメージに名前とタグを付ける
- `.`: Dockerfileがあるディレクトリ（ビルドコンテキスト）を指定する

```bash
docker images
```

`sample-app:1.0` が一覧に追加されていることを確認します。

## レイヤキャッシュの考え方

Dockerfileの各命令（`FROM`, `COPY`, `RUN`など）は「レイヤ」として積み重なり、それぞれがキャッシュされます。変更されていない行より前のレイヤはキャッシュが再利用されるため、**変更頻度が低い命令を上に、変更頻度が高い命令を下に書く**とビルドが高速化されます。

例えば依存パッケージのインストールとソースコードのコピーを分けて書くと、ソースコードだけを変更した際に依存パッケージの再インストールが走らずに済みます。

```dockerfile
FROM node:20-bookworm
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
```

## 演習

1. `nginx:latest` をpullし、`docker images`で確認する
2. 上記の`sample-app:1.0`イメージをビルドする
3. `Dockerfile`の`COPY`の順序を変えてもう一度ビルドし、キャッシュの効き方の違いを`docker build`の出力ログで観察する

## 章末チェックリスト

- [ ] `docker pull` と `docker images` でイメージを確認できた
- [ ] `Dockerfile`から`docker build`でイメージを作成できた
- [ ] レイヤキャッシュを意識した命令の並び順を説明できる

## 次へ

- [02-container-lifecycle.md](./02-container-lifecycle.md)
