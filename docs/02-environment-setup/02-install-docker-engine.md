# 02 Docker Engineのインストール

## 所要時間

約25分

## 学習目標

- WSL（Ubuntu）内にDocker Engineを直接インストールできる
- `docker`グループへの追加でsudoなしにDockerを使えるようにできる
- Dockerデーモンを起動し、動作確認できる

---

## なぜDocker Desktopを使わないか

01章で比較した通り、本教材では内部の仕組みが見える学習効果と、企業ライセンス費用の回避という理由から、WSL内へのDocker Engine直接インストールを主軸にします。Docker Desktopとの詳細な比較は [04-docker-desktop-comparison.md](./04-docker-desktop-comparison.md) で扱います。

## Docker Engineのインストール手順

WSLのUbuntuターミナルを開き、公式リポジトリからDocker Engineをインストールします。

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

`docker-compose-plugin` も同時に入れています。これにより06章で使う `docker compose`（ハイフンなしのサブコマンド形式）が使えるようになります。

### なぜ`apt install docker.io`ではなくこの手順なのか

Ubuntuの標準リポジトリにも`docker.io`という名前でDockerパッケージが存在しますが、本教材ではあえて使いません。標準リポジトリのバージョンはUbuntu側の検証・同期の都合でDocker公式のリリースより古くなりがちで、`docker-buildx-plugin`や`docker-compose-plugin`（`docker compose`のサブコマンド形式）が含まれていないことも多いためです。そこで上記の手順では、Docker社が配布する公式リポジトリを新たにaptへ登録し、そこから最新版をインストールしています。

### 各コマンドが何をしているか

| コマンド                                               | 役割                                                                                                                                  |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------- |
| `apt-get install ca-certificates curl gnupg`         | 後続の手順で使う3点セットを用意（HTTPS証明書検証・ダウンロード・GPG鍵の処理）                                                         |
| `install -m 0755 -d /etc/apt/keyrings`               | 信頼済みGPG鍵を置く専用ディレクトリを作成（`0755`＝apt実行時の非特権ユーザーからも読み取れる権限）                                  |
| `curl ... \| gpg --dearmor -o ...`                    | Docker公式のGPG公開鍵をダウンロードし、テキスト形式（ASCII armor）からapt用のバイナリ形式に変換して保存                               |
| `chmod a+r`                                          | 鍵ファイルを全ユーザーが読める状態にする（aptは専用の低権限ユーザーでリポジトリ検証を行うため）                                       |
| `echo ... \| tee /etc/apt/sources.list.d/docker.list` | 「このリポジトリはこのGPG鍵（`signed-by=`）で署名されたパッケージだけを信頼する」という設定を新規のリポジトリ定義ファイルとして追加 |
| 2回目の`apt-get update`                              | 直前に追加したDocker公式リポジトリの情報を読み込み直す（これをしないと新しいリポジトリの存在自体をaptがまだ認識していない）           |

`signed-by=/etc/apt/keyrings/docker.gpg`の部分は、「このリポジトリから取得したパッケージの署名は、このファイルの鍵で検証する」という指定です。GPG鍵とリポジトリ定義を1対1で明示的に紐付けることで、万一他のリポジトリ用に登録した別の鍵が誤って流用される事態を防いでいます。

またリポジトリURL内の`$(dpkg --print-architecture)`（例: `amd64`）と`$(. /etc/os-release && echo "$VERSION_CODENAME")`（例: `jammy`）は、値をハードコードせず実行時のシステムから動的に取得しています。同じ手順書をどのアーキテクチャ・どのUbuntuバージョンでも使い回せるようにするための工夫です。

## dockerグループへの追加（sudoなしでdockerを使う）

```bash
sudo usermod -aG docker $USER
```

⚠️ この変更はすぐには反映されません。一度WSLを完全に終了させる必要があります。**Windows側のPowerShell**で以下を実行してから、ターミナルを開き直してください。

```powershell
wsl --shutdown
```

💡 `wsl --shutdown`自体は終了コマンドに過ぎず、設定や変更を読み込み直す処理は含まれていません。次にWSLを起動する際にVMがゼロから作り直されることで、`docker`グループへの追加のような変更が反映される、という仕組みは [01-install-wsl.md](./01-install-wsl.md) で詳しく説明しています。

## Dockerデーモンの起動

WSLでは、ディストロによってsystemdが既定で無効になっている場合があります。

### なぜWSLでは既定でsystemdが無効なのか

通常のLinuxマシンは、電源を入れると`systemd`（PID 1）がハードウェア初期化からネットワーク・各種サービスの起動までを一括管理する「フルブート」を経ます。一方WSLのディストロは、そうしたフルブートを経ずに**`wsl`コマンドを叩いた瞬間にオンデマンドで起動する**、という軽量な起動モデルを採用しています。

`systemd`のサポート自体は比較的新しく追加された機能で、既定で無効になっているのは主に後方互換性のためです。多くの既存ディストロイメージやツールは「WSL内に`systemd`はいない」という前提で作られてきたため、有効化はデフォルトではなくオプトイン（`/etc/wsl.conf`での明示的な設定）という形になっています。

以下の2パターンのどちらかで起動します。

### パターンA: 都度起動する（手軽だが再起動のたびに必要）

```bash
sudo service docker start
```

WSLを再起動する（`wsl --shutdown`する）たびに、このコマンドを再実行する必要があります。

### パターンB: systemdを有効化して自動起動させる（推奨）

`/etc/wsl.conf` を編集（無ければ新規作成）し、以下を追記します。

```ini
[boot]
systemd=true
```

編集後、Windows側で `wsl --shutdown` を実行してWSLを再起動します。再度ターミナルを開いたら、以下でDockerを有効化・起動します。

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

以降はWSL起動時に自動的にDockerデーモンが立ち上がるようになります。

## 動作確認

```bash
docker version
```

`Client`と`Server`の両方のバージョン情報が表示されれば、CLIとデーモンの両方が正常に動作しています。

```bash
docker run hello-world
```

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

このメッセージが表示されれば、イメージのpull・コンテナの起動・終了までの一連の流れが正常に動作しています。

### 実行後の後始末は必要か

`docker run`は既定では**終了後もコンテナを削除しません**。実行後に確認すると、終了済み（`Exited`）状態のコンテナが残っていることが分かります。

```bash
docker ps -a
```

```
CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     NAMES
xxxxxxxxxxxx   hello-world   "/hello"   1 minute ago    Exited (0) 1 minute ago    happy_lovelace
```

これは不具合ではなく仕様です。**コンテナが失敗して終了した場合に、後から`docker logs <ID>`で原因を調べられるようにするため**、明示的に削除しない限り残すという設計になっています。もし終了と同時に自動消滅する仕様だったら、失敗時の調査ができなくなってしまいます。

`hello-world`イメージ自体も十数KB程度と小さく、コンテナのメタデータも軽量なので、この段階で放置してもディスク容量的な実害はほとんどありません。ただし気になる場合や、後始末の練習として以下のコマンドも押さえておくと良いでしょう。

```bash
# 個別に削除
docker rm <CONTAINER ID>

# 終了済みコンテナを一括削除
docker container prune

# 最初から使い捨てにしたい場合（終了と同時に自動削除）
docker run --rm hello-world
```

06章以降で複数のコンテナを繰り返し起動していくと、この「終了済みコンテナが黙って蓄積していく」性質がディスク圧迫や紛らわしさの原因になっていきます。

## 演習

1. Docker Engineをインストールする
2. `docker`グループへの追加を行い、`wsl --shutdown`後に`sudo`なしで`docker ps`が実行できることを確認する
3. パターンBのsystemd設定を行い、WSLを再起動してもDockerデーモンが自動起動することを確認する

## 章末チェックリスト

- [ ] `docker run hello-world` が成功した
- [ ] `sudo`なしで`docker`コマンドが実行できる
- [ ] WSL再起動後もDockerデーモンが自動起動する設定ができた
- [ ] WSLで既定でsystemdが無効になっている理由を説明できる
- [ ] `apt install docker.io`ではなくDocker公式リポジトリを使う理由を説明できる
- [ ] `signed-by=`によるGPG鍵とリポジトリの紐付けの目的を説明できる
- [ ] `docker run`後にコンテナが自動削除されない理由と、`docker rm`/`--rm`の使い分けを説明できる

## 次へ

- [03-vscode-remote-wsl.md](./03-vscode-remote-wsl.md)
