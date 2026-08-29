# 04 ネットワークの基本（導入）

## 所要時間

約15分

## 学習目標

- Dockerが既定で作るネットワークの種類を知る
- コンテナ間通信の最小構成を体験する

---

## Dockerが作るネットワークの種類

```bash
docker network ls
```

```
NETWORK ID     NAME      DRIVER    SCOPE
b1c2d3e4f5a6   bridge    bridge    local
7a8b9c0d1e2f   host      host      local
3f4e5d6c7b8a   none      null      local
```

Docker Engineをインストールすると、既定で3種類のネットワークが作られます。

| 名前 | 概要 |
|---|---|
| `bridge` | コンテナ起動時に指定しなければ使われる既定のネットワーク |
| `host` | コンテナがホストのネットワークスタックをそのまま共有する特殊モード |
| `none` | ネットワーク接続を持たない |

## コンテナ間通信の最小確認

まず、ユーザー定義のbridgeネットワークを作成します。

```bash
docker network create sample-net
```

2つのコンテナを、このネットワークに接続して起動します。

```bash
docker run -d --name web1 --network sample-net nginx
docker run -it --name web2 --network sample-net ubuntu bash
```

`web2`のコンテナ内から、`web1`という**コンテナ名**で通信できることを確認します。

```bash
apt-get update && apt-get install -y iputils-ping
ping web1
```

```
PING web1 (172.x.x.x): 56 data bytes
64 bytes from 172.x.x.x: icmp_seq=0 ttl=64 time=0.123 ms
```

IPアドレスを指定していないのに、コンテナ名だけで通信できました。これはユーザー定義のbridgeネットワークが持つDNS機能によるものです。

```bash
exit
docker rm -f web1 web2
docker network rm sample-net
```

## ここで止める理由

ここまでで「コンテナ名で通信できる」という事実は確認できましたが、以下のような疑問はまだ解決していません。

- なぜWindows側から`localhost:8080`でコンテナにアクセスできるのか
- WSL2のネットワークモードとDockerのネットワークはどう関係しているのか
- 既定の`bridge`とユーザー定義の`bridge`で、なぜ名前解決の可否が違うのか

これらは「Windows・WSL2・Docker」の層構造を理解しないと正確に説明できません。次の [04-networking-deep-dive](../04-networking-deep-dive/README.md) で、概念から層構造、実際の通信経路まで順を追って解説します。

## 章末チェックリスト

- [ ] `docker network ls` で3種類の既定ネットワークを確認できた
- [ ] ユーザー定義bridgeでコンテナ名による通信ができることを確認できた

## 次へ

- [04-networking-deep-dive/README.md](../04-networking-deep-dive/README.md)
