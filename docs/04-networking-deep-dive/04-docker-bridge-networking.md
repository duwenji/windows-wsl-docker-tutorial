# 04 Dockerブリッジネットワークの内部構造

## 所要時間

約25分

## 学習目標

- `docker0`ブリッジとユーザー定義bridgeの違いを説明できる
- `docker network inspect`でネットワークの内部構造を読める
- WSL2 VM内にbridgeインターフェースが実在することを確認できる

---

## docker0ブリッジ

Docker Engineをインストールすると、既定の`bridge`ネットワークの実体として`docker0`という名前の仮想ブリッジが自動的に作られます。これはWSL2 VM（②の層）の中に存在する、通常のLinuxネットワークインターフェースです。

```bash
ip addr show docker0
```

```
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 ...
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
```

## コンテナへのIP割当

コンテナを起動すると、そのコンテナのnetwork namespaceにveth pairの片割れが挿入され、`docker0`（または指定したネットワーク）のサブネットからIPアドレスが1つ割り当てられます。

```bash
docker run -d --name web nginx
docker inspect web --format '{{.NetworkSettings.IPAddress}}'
```

```
172.17.0.2
```

## ユーザー定義bridgeを推奨する理由

既定の`bridge`（`docker0`）に接続したコンテナ同士は、IPアドレスでは通信できますが、**コンテナ名による名前解決ができません**。これに対し、`docker network create`で作成したユーザー定義bridgeには、DockerEngine内蔵のDNS機能が有効になっており、コンテナ名で名前解決できます。

これは03章の演習（`docker network create sample-net`でping確認した内容）の裏側にある仕組みです。実務では、複数コンテナを連携させる場合は必ずユーザー定義bridgeを使うことが推奨されます（06章のCompose章でも、この仕組みが標準で使われます）。

## docker network inspectで見る内部構造

```bash
docker network create sample-net
docker run -d --name web --network sample-net nginx
docker network inspect sample-net
```

```json
[
    {
        "Name": "sample-net",
        "Driver": "bridge",
        "IPAM": {
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Containers": {
            "a1b2c3...": {
                "Name": "web",
                "IPv4Address": "172.18.0.2/16"
            }
        }
    }
]
```

- `IPAM.Config` — このネットワークに割り当てられたサブネットとゲートウェイ
- `Containers` — 現在このネットワークに接続しているコンテナと、それぞれのIPアドレス

## ホスト側から見たbridgeの実体

WSL2 VM（②の層）はれっきとした1つのLinuxマシンです。そのため、Dockerが作ったbridgeは、通常のLinuxネットワーク管理コマンドでそのまま確認できます。

```bash
ip addr show
ip link show type bridge
```

`docker0`に加えて、`br-`から始まるユーザー定義ネットワーク用のインターフェースも見えるはずです。これは、02章で学んだ「WSL2 VMは本物のLinuxカーネルを持つ」という事実の裏付けでもあります。

## 演習

1. `docker network create sample-net` でユーザー定義bridgeを作成する
2. コンテナを1つ起動して接続し、`docker network inspect sample-net`でIPアドレスを確認する
3. WSL側で `ip link show type bridge` を実行し、`docker0`と`br-`で始まるインターフェースの両方が存在することを確認する

## 章末チェックリスト

- [ ] `docker0`がWSL2 VM内の通常のLinuxブリッジであることを説明できる
- [ ] 既定bridgeとユーザー定義bridgeで名前解決の可否が違う理由を説明できる
- [ ] `docker network inspect`の出力からサブネットとコンテナIPを読み取れる

## 次へ

- [05-port-publishing-path.md](./05-port-publishing-path.md)
