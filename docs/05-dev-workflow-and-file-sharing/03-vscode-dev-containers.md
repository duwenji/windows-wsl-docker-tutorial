# 03 VS Code Dev Containersでの開発ループ

## 所要時間

約25分

## 学習目標

- devcontainer.jsonの最小構成を作成できる
- Dev Containersでコンテナ内開発を起動できる
- Remote-WSLとの組み合わせで高速な開発ループを構築できる

---

## Dev Containersとは

Dev Containersは、プロジェクトに`.devcontainer/devcontainer.json`を置くことで、VS Code自体をコンテナ内で動かし、拡張機能・ターミナル・デバッガまで含めてコンテナ内の環境で開発できるようにする仕組みです（02章でインストール済みの拡張機能を使用します）。

## 最小構成のdevcontainer.json

```bash
mkdir -p ~/projects/sample-dev/.devcontainer
```

`~/projects/sample-dev/.devcontainer/devcontainer.json`:

```json
{
  "name": "sample-dev",
  "image": "node:20-bookworm",
  "workspaceFolder": "/workspace",
  "forwardPorts": [3000]
}
```

- `image`: ベースとなるDockerイメージ
- `workspaceFolder`: コンテナ内でプロジェクトを配置するパス
- `forwardPorts`: コンテナ内で使うポートをホスト側にも自動転送する設定（04章で学んだ`-p`相当をVS Codeが自動で行う）

## 起動確認

1. Remote-WSL拡張で `~/projects/sample-dev` をVS Codeで開く
2. コマンドパレット（`Ctrl+Shift+P`）から `Dev Containers: Reopen in Container` を実行する
3. 初回はイメージのpullとコンテナの起動が行われ、しばらく待つと統合ターミナルが開く

統合ターミナルで以下を実行し、コンテナ内のシェルであることを確認します。

```bash
node --version
cat /etc/os-release
```

Node.jsのバージョンが`devcontainer.json`で指定した`20`系であること、OSがコンテナのベースイメージ（Debian bookworm）であることを確認します。

## Remote-WSLとの関係

Dev Containersは、bind mount元としてプロジェクトフォルダをそのままコンテナに接続します。前のレッスンで学んだ通り、**Remote-WSLでWSL内フォルダ（`~/projects/sample-dev`）を開いた状態からDev Containersを起動すると、bind mount元がWSL側パスになり、高速な経路で開発できます**。

逆に、Windows側のフォルダ（`C:\Users\...\projects\sample-dev`）を直接開いてDev Containersを起動すると、bind mount元がWindows側パスになり、性能低下の原因になります。**必ずRemote-WSLでWSL側フォルダを開いてからDev Containersを起動する**、という順序を徹底してください。

```mermaid
flowchart TB
    Start(["sample-devプロジェクトを開く"]) --> Q{"どちらのパスから開いたか"}

    Q -->|"WSL側パス<br/>~/projects/sample-dev"| Good["Remote-WSLでWSL側フォルダを開く<br/>（左下: WSL: Ubuntu）"]
    Q -->|"Windows側パス<br/>C:\Users\...\projects\sample-dev"| Bad["Windowsから直接フォルダを開く"]

    Good --> ReopenGood["Dev Containers: Reopen in Container"]
    Bad --> ReopenBad["Dev Containers: Reopen in Container"]

    ReopenGood --> MountGood["bind mount元 = WSL側パス<br/>（ネイティブLinuxファイルシステム）"]
    ReopenBad --> MountBad["bind mount元 = Windows側パス<br/>（9pプロトコル越し）"]

    MountGood --> FastNote["高速な開発ループ"]
    MountBad --> SlowNote["性能低下<br/>（node_modules等の大量ファイル操作で顕著）"]
```

同じ`Dev Containers: Reopen in Container`という操作でも、**その直前にどちらのファイルシステムパスでフォルダを開いていたか**によって、bind mount元がまるごと変わってしまう点に注意してください。

## 演習

1. `sample-dev`プロジェクトを作成し、Remote-WSLでWSL側フォルダとして開く
2. `Dev Containers: Reopen in Container`でコンテナ内開発環境を起動する
3. 統合ターミナルでコンテナ内であることを確認する

## 章末チェックリスト

- [ ] `devcontainer.json`の最小構成を作成できた
- [ ] Dev Containersでコンテナ内開発環境を起動できた
- [ ] Remote-WSL経由で開くことの重要性を説明できる

## 次へ

- [04-line-endings-and-git.md](./04-line-endings-and-git.md)
