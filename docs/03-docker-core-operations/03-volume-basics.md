# 03 ボリュームの基本

## 所要時間

約20分

## 学習目標

- コンテナ削除でデータが失われることを体験する
- named volumeとbind mountの違いを説明できる

---

## コンテナのファイルは消える

まず、データが失われる様子を実際に確認します。

```bash
docker run -it --name test-container ubuntu bash
```

コンテナ内で:

```bash
echo "hello" > /data.txt
exit
```

コンテナを削除して、もう一度同じイメージから起動します。

```bash
docker rm test-container
docker run -it --name test-container ubuntu bash
cat /data.txt   # → No such file or directory
exit
docker rm test-container
```

コンテナ内で作成したファイルは、そのコンテナ固有の書き込み可能レイヤに保存されており、**コンテナを削除すると一緒に消えます**。これがボリュームが必要な理由です。

## named volume

Docker Engine自身が管理する永続化領域です。

```bash
docker volume create myvol
docker run -it --name test2 -v myvol:/data ubuntu bash
```

コンテナ内で:

```bash
echo "hello" > /data/data.txt
exit
```

```bash
docker rm test2
docker run -it --name test2 -v myvol:/data ubuntu bash
cat /data/data.txt   # → hello （残っている）
exit
docker rm test2
```

```bash
docker volume inspect myvol
```

`Mountpoint`にホスト（WSL2 VM）側の実体パスが表示されます。

## bind mount

ホスト（WSL）側の任意のディレクトリを、コンテナ内の任意のパスにそのまま接続します。

```bash
mkdir -p ~/projects/bind-demo
echo "from host" > ~/projects/bind-demo/note.txt
docker run -it --name test3 -v ~/projects/bind-demo:/data ubuntu bash
cat /data/note.txt   # → from host
exit
docker rm test3
```

⚠️ ここでは `~/projects/bind-demo`（WSL側のパス）を使っています。**Windows側のパス（`/mnt/c/...`）をbind mountすると性能が大きく落ちる**という重要な注意点があります。詳細は [05-dev-workflow-and-file-sharing](../05-dev-workflow-and-file-sharing/README.md) で扱います。

## named volume vs bind mountの使い分け

| 観点 | named volume | bind mount |
|---|---|---|
| 管理者 | Docker Engineが管理 | ユーザーが指定したホスト側パス |
| 用途の典型例 | DBデータなどの永続化 | 開発中のソースコードの共有 |
| ホスト側から直接編集 | しにくい（`docker volume inspect`で場所を調べる必要がある） | しやすい（普段使っているエディタで直接編集できる） |
| 性能（WSL2上） | 常に高速（WSL2 VM内のファイルシステムに直接存在） | ホスト側パスの場所次第（WSL側パスなら高速、Windows側パスは低速） |

## 演習

1. `docker volume create` でnamed volumeを作り、データが永続化されることを確認する
2. `~/projects`配下でbind mountを試し、ホスト側で編集した内容がコンテナ内にも反映されることを確認する

## 章末チェックリスト

- [ ] コンテナ削除でデータが消える現象を再現できた
- [ ] named volumeでデータが永続化されることを確認できた
- [ ] named volumeとbind mountの使い分けを説明できる

## 次へ

- [04-network-basics-intro.md](./04-network-basics-intro.md)
