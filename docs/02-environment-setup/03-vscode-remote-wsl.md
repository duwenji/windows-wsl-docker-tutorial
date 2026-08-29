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

## Dev Containers拡張のインストール

拡張機能 `ms-vscode-remote.remote-containers` をインストールします。この拡張は、プロジェクトの`.devcontainer/devcontainer.json`定義に基づいて、コンテナ内で直接開発する環境を構築するためのものです。詳しい使い方は [05-vscode-dev-containers](../05-dev-workflow-and-file-sharing/03-vscode-dev-containers.md) で扱います。

## Docker拡張（任意）

拡張機能 `ms-azuretools.vscode-docker` は必須ではありませんが、イメージ・コンテナ・ボリュームをGUIで一覧確認したい場合に便利です。CLI操作に慣れるまでの補助として使うと良いでしょう。

## 演習

1. Remote-WSL拡張をインストールし、WSL内の適当なフォルダで `code .` を実行してVS Codeが起動することを確認する
2. ウィンドウ左下に `WSL: Ubuntu` と表示されていることを確認する
3. Dev Containers拡張をインストールする

## 章末チェックリスト

- [ ] `code .` でWSL内のフォルダをVS Codeで開けた
- [ ] ウィンドウ左下に `WSL: Ubuntu` と表示されている
- [ ] Dev Containers拡張がインストール済みである

## 次へ

- [04-docker-desktop-comparison.md](./04-docker-desktop-comparison.md)
