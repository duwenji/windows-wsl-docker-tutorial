# 01 WSLのインストール

## 所要時間

約20分

## 学習目標

- `wsl --install` でWSL2環境を構築できる
- WSL2がデフォルトになっていることを確認できる
- `.wslconfig`の存在意義を理解する

---

## 前提条件

- Windows 11（Windows 10の場合はビルドバージョンによって手順が異なるため、Microsoft公式ドキュメントを参照してください）
- BIOS/UEFIで仮想化支援機能（Intel VT-x / AMD-V）が有効になっていること

## WSLのインストール

PowerShellを**管理者権限**で開き、以下を実行します。

```powershell
wsl --install
```

このコマンド一発で、WSLの有効化・Linuxカーネルの最新化・既定ディストロ（Ubuntu）のインストールまで自動的に行われます。完了後、PCの再起動を求められる場合があります。

## ディストリビューションを指定する場合

既定以外のディストロを使いたい場合は、まず利用可能な一覧を確認します。

```powershell
wsl -l -o
```

```
インストールできる有効なディストリビューションの一覧です。
'wsl.exe --install <Distro>' を使用してインストールします。

NAME                                   FRIENDLY NAME
Ubuntu                                 Ubuntu
Ubuntu-22.04                           Ubuntu 22.04 LTS
Debian                                 Debian GNU/Linux
```

バージョンを指定してインストールする例:

```powershell
wsl --install -d Ubuntu-22.04
```

本教材では以降、既定の `Ubuntu` を前提に手順を記載します。

## WSL2がデフォルトであることの確認

```powershell
wsl -l -v
```

```
  NAME      STATE           VERSION
* Ubuntu    Running         2
```

`VERSION`列が`2`になっていない場合は、以下のコマンドで明示的にバージョンを切り替えます。

```powershell
wsl --set-version Ubuntu 2
```

## リソース制限の初期設定

WSL2はデフォルトでは搭載メモリの大部分を確保できてしまうことがあります。最低限の初期設定として、ユーザーフォルダに `.wslconfig` を作成しておきます。

ファイルの場所: `%UserProfile%\.wslconfig`（例: `C:\Users\<ユーザー名>\.wslconfig`）

### なぜユーザーフォルダに置くのか

`.wslconfig`が制御しているのは、01章で学んだ「複数のディストロが共有する1つのWSL2 VM」そのものです。このVMは**Windowsのユーザーセッションごとに**起動されるため、設定もWindowsユーザーに紐づく場所（`%UserProfile%`）に置く仕組みになっています。同じPCに複数のWindowsアカウントがあっても、それぞれ独立したWSL2 VM・独立したリソース設定を持てるということです。

（この後インストールするDocker Engineの設定で登場する `/etc/wsl.conf` は、これとは階層が異なり、各ディストロの内部に置く「そのディストロ固有」の設定ファイルです。混同しないよう注意してください。）

また、`%UserProfile%`はそのユーザーが常に書き込み権限を持つ場所なので、`.wslconfig`の作成・編集に**管理者権限は不要**です。仮にシステム全体の共有領域（`C:\ProgramData\`など）に置く仕様だったら、変更のたびに管理者権限が必要になっていたはずです。

```ini
[wsl2]
memory=4GB
processors=2
```

⚠️ この設定はあくまで初期値です。メモリ肥大化（`Vmmem`問題）への詳しい対処は [07-operations-and-troubleshooting](../07-operations-and-troubleshooting/README.md) で扱います。

## 演習

1. `wsl --install` でWSLをインストールする（インストール済みの場合は `wsl --status` で状態を確認する）
2. `wsl -l -v` を実行し、`VERSION`列が`2`であることを確認する
3. `.wslconfig` を作成し、`wsl --shutdown` 実行後に再度 `wsl -l -v` が問題なく動くことを確認する

## 章末チェックリスト

- [ ] `wsl -l -v` でWSL2ディストロが起動していることを確認した
- [ ] `.wslconfig` の役割と設置場所を説明できる
- [ ] `.wslconfig`がユーザーフォルダに置かれる理由を、`wsl.conf`との違いを含めて説明できる

## 次へ

- [02-install-docker-engine.md](./02-install-docker-engine.md)
