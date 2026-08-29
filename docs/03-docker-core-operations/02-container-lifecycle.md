# 02 コンテナのライフサイクル

## 所要時間

約20分

## 学習目標

- コンテナの起動・停止・削除の一連の操作ができる
- ログ確認とコンテナ内への接続ができる

---

## コンテナの起動

```bash
docker run -d --name web -p 8080:80 nginx
```

- `-d`: バックグラウンドで実行する（detached）
- `--name web`: コンテナに名前を付ける（省略するとランダムな名前が自動生成される）
- `-p 8080:80`: Windows/WSL側の8080番ポートを、コンテナ内の80番ポートに転送する

## 状態確認

```bash
# 起動中のコンテナ一覧
docker ps
```

```
CONTAINER ID   IMAGE   COMMAND                  STATUS         PORTS                  NAMES
a1b2c3d4e5f6   nginx   "/docker-entrypoint.…"   Up 2 minutes   0.0.0.0:8080->80/tcp   web
```

```bash
# 停止中も含めた全コンテナ一覧
docker ps -a

# ログ確認
docker logs web

# ログをリアルタイムで追跡
docker logs -f web
```

## コンテナへの接続

コンテナ内部のシェルに入って直接確認したい場合は`exec`を使います。

```bash
docker exec -it web bash
```

- `-i`: 標準入力を開いたままにする
- `-t`: 疑似端末を割り当てる

コンテナ内から抜けるには`exit`と入力します。

## 停止・再起動・削除

```bash
docker stop web      # 停止
docker start web     # 再起動
docker restart web   # 再起動（停止+起動をまとめて実行）
docker rm web         # 削除（停止済みのコンテナのみ削除可能）
docker rm -f web      # 強制削除（起動中でも削除する）
```

## 演習

1. `docker run -d --name web -p 8080:80 nginx` でコンテナを起動する
2. Windows側のブラウザで `http://localhost:8080` を開き、nginxの初期ページが表示されることを確認する
3. `docker exec -it web bash` でコンテナ内に入り、`/usr/share/nginx/html/`の中身を確認してから`exit`で抜ける
4. `docker stop web` → `docker rm web` の順でコンテナを削除する

⚠️ ここでは「Windows側から`localhost:8080`でアクセスできる」という結果だけを確認します。**なぜそれが可能なのか**という仕組み（WSL2のネットワークモード、ポート転送の経路）は [04-networking-deep-dive](../04-networking-deep-dive/README.md) で詳しく扱います。

## 章末チェックリスト

- [ ] コンテナを起動し、Windows側ブラウザからアクセスできた
- [ ] `docker exec`でコンテナ内シェルに入れた
- [ ] `docker stop` / `docker rm` でコンテナを削除できた

## 次へ

- [03-volume-basics.md](./03-volume-basics.md)
