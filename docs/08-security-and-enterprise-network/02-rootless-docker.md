# 02 Rootless Docker

## 所要時間

約15分

## 学習目標

- rootless modeが必要になる背景を説明できる
- rootless modeの制約を理解する

---

## なぜrootlessが必要になる場面があるか

02章でセットアップした既定のDocker Engineは、`dockerd`自体がroot権限で動作します。`docker`グループに所属するユーザーは、直接`sudo`を使わなくても、実質的にroot相当の操作が可能になってしまいます。

### 具体的にどう「root相当」になるのか

抽象的な話に聞こえるかもしれませんが、実際に試すと一目瞭然です。

```bash
docker run -it -v /:/hostroot ubuntu bash
```

このコマンドは、ホスト（WSL2 VM）の**ルートディレクトリ全体**をコンテナ内の`/hostroot`にbind mountしています。コンテナ内のプロセスはroot権限で動いているため、コンテナ内から以下のようにホスト側の任意のファイルを読み書きできてしまいます。

```bash
cat /hostroot/etc/shadow
```

`sudo`を一度も使っていないにもかかわらず、通常は`sudo`が無いと読めないファイルにアクセスできました。これは、`dockerd`自体がroot権限で動作しており、「`docker`コマンドを実行できる＝`dockerd`にホストの任意のマウント・root実行を指示できる」ため、`docker`グループへの所属が実質的にroot権限そのものと等価になるからです。

セキュリティ要件が厳しい環境では、この「dockerグループ所属＝root相当」という構図自体がリスクと見なされる場合があります。これを解決するのが**rootless mode**です。rootless modeでは、`dockerd`自体が一般ユーザー権限で動作し、root権限を必要としません（`dockerd`自体が一般ユーザー権限しか持たないため、上記のような操作をしてもホスト側でroot相当の被害は生じません）。

## rootless modeのインストール

```bash
curl -fsSL https://get.docker.com/rootless | sh
```

または、公式スクリプトを使う方法もあります。

```bash
dockerd-rootless-setuptool.sh install
```

インストール後、既定インストール（root権限で動く`dockerd`）とは別のソケット・別の設定で動作します。両者を同時に使う場合は、`DOCKER_HOST`環境変数でどちらに接続するかを切り替えます。

## rootless modeの制約

- 1024番未満の特権ポートへの直接bindができない場合がある（回避策として`slirp4netns`のポートフォワーディング機能や`CAP_NET_BIND_SERVICE`の付与などがある）
- 一部のネットワークドライバやストレージドライバに制約がある
- 既定インストールとの共存はできるが、設定が複雑になる

## この教材での位置づけ

rootless modeは実務で必須というわけではなく、**セキュリティ要件が特に厳しい環境向けの選択肢**です。本教材のこれまでの章は、すべて既定（root権限の`dockerd`）のインストールを前提にしています。rootless modeを検討する際は、まず自組織のセキュリティポリシーで実際に要求されているかを確認してから導入することを推奨します。

## 章末チェックリスト

- [ ] `docker`グループ所属がroot相当の権限を持つことを、具体的な操作例（`-v /:/hostroot`）で説明できる
- [ ] rootless modeの制約を説明できる
- [ ] rootless modeが必須ではなく選択肢の1つであることを理解している

## 次へ

- [03-secrets-management.md](./03-secrets-management.md)
