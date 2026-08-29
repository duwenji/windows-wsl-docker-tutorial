# 03 VS Code + Remote-WSL連携

## 所要時間

約10分

## 学習目標

- VS CodeからWSL内のプロジェクトを直接開けるようにする
- Dev Containers拡張をインストールする（05章で本格的に使う準備）

---

## VS Code + Remote-WSL拡張のインストール

Windows側のVS Codeに、拡張機能 `ms-vscode-remote.remote-wsl` をインストールします。

インストール後、WSL（Ubuntu）のターミナルからプロジェクトフォルダで以下を実行すると、VS CodeがWSL内のファイルを直接編集するモードで起動します。

```bash
cd ~/projects/sample-app
code .
```

初回起動時、WSL側にVS Codeサーバーが自動的にインストールされます。ウィンドウ左下に `WSL: Ubuntu` と表示されていれば、WSL内のファイルシステムを直接見ている状態です。

💡 [05-dev-workflow-and-file-sharing](../05-dev-workflow-and-file-sharing/README.md) で説明する通り、プロジェクトはWindows側（`/mnt/c/...`）ではなくWSL側のファイルシステム（`/home/<user>/...`）に置くことを推奨します。

## WSLから起動した場合とWindowsから直接起動した場合の違い

同じVS Codeでも、`code .` をWSLのターミナルから実行するか、Windows側から直接起動するかで内部の動作が変わります。

WSLから起動すると、`ms-vscode-remote.remote-wsl` 拡張が自動的に働き、**UI（クライアント）はWindows側で表示されたまま、実体である「VS Codeサーバー」がWSL（Linux）側で起動する**構成になります。拡張機能・言語サーバー・デバッガなどはこのサーバー側、つまりWSL内で実行されます。

| 項目 | WSLから起動（`code .`） | Windowsから直接起動 |
|---|---|---|
| ファイルアクセス | WSL内のLinuxファイルシステムを直接読み書き | Windows側のファイルシステムを直接読み書き |
| 拡張機能の実行場所 | ほとんどがWSL側（Linuxバイナリ）で実行 | すべてWindows側で実行 |
| 統合ターミナルの既定シェル | bash/zshなどLinux側のシェル | PowerShell / コマンドプロンプト |
| 使われるnode/python/git等 | WSL内にインストールされたもの | Windowsにインストールされたもの |
| ウィンドウ左下の表示 | `WSL: Ubuntu` | 表示なし |

⚠️ 拡張機能はWindows側・WSL側で別々にインストールされます。普段Windowsから直接VS Codeを使っている場合、WSLから初めて `code .` を実行したときは、よく使う拡張機能（Prettier、ESLintなど）をWSL側にも入れ直す必要があります。

💡 このリポジトリのハンズオンはWSL内のDockerを操作するため、**WSLのターミナルから `code .` で起動する運用に統一**します。Windowsから直接起動したVS Codeで `/mnt/c/...` 経由の同じプロジェクトを開くこともできますが、9pプロトコル越しのアクセスになりパフォーマンスが落ちます（詳細は [05-dev-workflow-and-file-sharing/01-filesystem-boundary.md](../05-dev-workflow-and-file-sharing/01-filesystem-boundary.md)）。

## Dev Containers拡張のインストール

拡張機能 `ms-vscode-remote.remote-containers` をインストールします。この拡張は、プロジェクトの`.devcontainer/devcontainer.json`定義に基づいて、コンテナ内で直接開発する環境を構築するためのものです。詳しい使い方は [05-dev-workflow-and-file-sharing/03-vscode-dev-containers.md](../05-dev-workflow-and-file-sharing/03-vscode-dev-containers.md) で扱います。

## Docker拡張（任意）

拡張機能 `ms-azuretools.vscode-docker` は必須ではありませんが、イメージ・コンテナ・ボリュームをGUIで一覧確認したい場合に便利です。CLI操作に慣れるまでの補助として使うと良いでしょう。

## 演習

1. Remote-WSL拡張をインストールし、WSL内の適当なフォルダで `code .` を実行してVS Codeが起動することを確認する
2. ウィンドウ左下に `WSL: Ubuntu` と表示されていることを確認する
3. 統合ターミナルを開き、既定シェルがbash/zshになっていることを確認する
4. Dev Containers拡張をインストールする

## 章末チェックリスト

- [ ] `code .` でWSL内のフォルダをVS Codeで開けた
- [ ] ウィンドウ左下に `WSL: Ubuntu` と表示されている
- [ ] WSLから起動した場合とWindowsから直接起動した場合で、拡張機能の実行場所とターミナルの既定シェルが変わることを説明できる
- [ ] Dev Containers拡張がインストール済みである

## 次へ

- [04-docker-desktop-comparison.md](./04-docker-desktop-comparison.md)
