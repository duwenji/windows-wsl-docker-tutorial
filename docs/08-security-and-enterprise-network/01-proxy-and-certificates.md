# 01 企業プロキシと証明書対応

## 所要時間

約25分

## 学習目標

- WSL・apt・Dockerそれぞれのプロキシ設定を実施できる
- ビルド時プロキシと社内CA証明書の組み込みができる

---

## WSL（Ubuntu）側のプロキシ設定

`/etc/environment`に追記します（全ユーザー・全プロセス共通の設定）。

```
http_proxy=http://proxy.example.com:8080
https_proxy=http://proxy.example.com:8080
no_proxy=localhost,127.0.0.1,.example.internal
```

シェルの現在のセッションにも反映させたい場合は`~/.bashrc`にも追記します。

```bash
export http_proxy=http://proxy.example.com:8080
export https_proxy=http://proxy.example.com:8080
export no_proxy=localhost,127.0.0.1,.example.internal
```

## apt用プロキシ設定

`/etc/apt/apt.conf.d/proxy.conf`:

```
Acquire::http::Proxy "http://proxy.example.com:8080";
Acquire::https::Proxy "http://proxy.example.com:8080";
```

## Docker Engine（dockerd）用プロキシ設定

`dockerd`自体がイメージをpullする際にもプロキシを経由させる必要がある場合、systemdのdrop-in設定を使います。

```bash
sudo mkdir -p /etc/systemd/system/docker.service.d
```

`/etc/systemd/system/docker.service.d/http-proxy.conf`:

```ini
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:8080"
Environment="HTTPS_PROXY=http://proxy.example.com:8080"
Environment="NO_PROXY=localhost,127.0.0.1"
```

設定を反映させます。

```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## ビルド時プロキシ（docker build時のみ有効にする方法）

コンテナ**内部**でのパッケージインストール等にプロキシが必要な場合は、`--build-arg`で渡します。

```bash
docker build \
  --build-arg HTTP_PROXY=http://proxy.example.com:8080 \
  --build-arg HTTPS_PROXY=http://proxy.example.com:8080 \
  -t sample-app:1.0 .
```

⚠️ プロキシのURLや認証情報をDockerfile内に直接`ENV`で書き込んではいけません。ビルド後のイメージレイヤに認証情報が残ってしまい、イメージを配布した相手に漏洩するリスクがあります。`--build-arg`は既定ではイメージのレイヤ履歴に残ってしまう点にも注意し、認証情報を含む場合は`--secret`機能（BuildKit）の利用を検討してください。

## 社内CA証明書の組み込み

社内独自のCA証明書を使ったTLS通信（社内Git、社内パッケージリポジトリなど）を行う場合、WSL側にも証明書を組み込む必要があります。

```bash
sudo cp company-ca.crt /usr/local/share/ca-certificates/
sudo update-ca-certificates
```

⚠️ **コンテナ内でも同じ証明書が必要な場合**、WSL側に証明書を入れただけでは不十分です。コンテナは別のファイルシステムを持つため、Dockerfile側でも明示的に組み込む必要があります。

```dockerfile
FROM ubuntu:22.04
COPY company-ca.crt /usr/local/share/ca-certificates/
RUN update-ca-certificates
```

## 演習

1. 自分の環境のプロキシ情報を確認し、`/etc/environment`と`apt`用の設定を行う
2. `dockerd`用のsystemd drop-in設定を作成し、`docker pull`がプロキシ経由で成功することを確認する
3. （社内CA証明書がある場合）WSL側とDockerfile側の両方に組み込む

## 章末チェックリスト

- [ ] WSL・apt・dockerdそれぞれのプロキシ設定の違いを説明できる
- [ ] Dockerfileに認証情報を直接書いてはいけない理由を説明できる
- [ ] コンテナ内にも証明書を別途組み込む必要がある理由を説明できる

## 次へ

- [02-rootless-docker.md](./02-rootless-docker.md)
