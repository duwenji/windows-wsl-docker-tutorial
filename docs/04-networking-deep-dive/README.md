# 04 Networking Deep Dive

## この章で学ぶこと

- Linuxのnetwork namespace / bridge / veth pairというコンテナネットワークの基礎
- Windows・WSL2 VM・Docker Engine・コンテナの4層構造
- WSL2自体のネットワーク方式（NATモード / mirrored networking mode）
- Dockerのブリッジネットワークの内部構造
- `-p`（ポート公開）が実際にどう動いているか
- ネットワークトラブルの層構造に沿った切り分け方

## 学習目標

- Windows・WSL2・Dockerの4層構造を自分の言葉で説明できる
- 「なぜWindows側からlocalhostでコンテナに繋がるのか」を仕組みから説明できる
- ネットワークトラブルが起きたとき、どの層が原因かを切り分けられる

## 演習案

1. `docker run -d --name web -p 8080:80 nginx` を起動し、`sudo iptables -t nat -L DOCKER -n`でDNATルールを確認する
2. `ip addr show docker0` と `ip addr show eth0` を実行し、②WSL2 VM内のネットワーク構成を確認する
3. `.wslconfig`で`networkingMode=mirrored`に切り替え、`wsl --shutdown`後にネットワークの見え方の違いを観察する

## 成功判定

- 4層構造の図を見ずに説明できる
- 「Windowsから繋がらない」というトラブルに対して、どの層から確認すべきか即座に判断できる

## 次章へ

- [05-dev-workflow-and-file-sharing/README.md](../05-dev-workflow-and-file-sharing/README.md)
