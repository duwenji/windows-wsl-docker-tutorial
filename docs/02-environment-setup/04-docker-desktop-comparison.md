# 04 比較コラム: Docker Desktop vs WSL+Docker Engine直接導入

## 所要時間

約10分

## 学習目標

- Docker DesktopとWSL+Docker Engine直接導入の違いを説明できる
- 実務でDocker Desktop環境に配属された場合に読み替えられる

---

## 比較コラム: Docker Desktop vs WSL+Docker Engine直接導入

| 観点 | Docker Desktop（WSL2 backend） | WSL + Docker Engine直接導入（本教材） |
|---|---|---|
| GUI | あり（コンテナ/イメージ管理画面） | なし（CLI操作） |
| ライセンス | 大企業（従業員251人以上または年間売上一定額以上が目安）では商用サブスクリプションが必要になる場合がある | 無料（Docker Engine自体はOSS） |
| リソース管理 | Docker Desktopの設定画面（GUI） | `.wslconfig`ファイルを直接編集 |
| 起動の自動化 | Windows起動時にDocker Desktopが自動起動する設定が可能 | systemd設定（本教材02章参照）または都度`service docker start` |
| 複数ディストロ間での共有 | 設定でディストロ横断のDocker統合が可能 | インストールしたディストロ内でのみ`docker`コマンドが使える |
| 内部構造の見えやすさ | GUIに隠蔽されている | `dockerd`の起動やネットワーク設定を直接触るため理解が深まる |

## 実務でDocker Desktop環境に配属された場合に読み替えるポイント

- `docker`コマンド自体は同じです。CLIの使い方はDocker Desktop環境でもWSL直接導入環境でも変わりません。
- リソース制限（メモリ・CPU）は、Docker Desktopの場合「設定」→「Resources」画面から行います。本教材で扱う`.wslconfig`による設定と役割は同じです。
- WSL統合（どのディストロでDockerコマンドを使えるようにするか）は「設定」→「Resources」→「WSL Integration」画面で切り替えます。

## このあとの章で前提とする環境

本教材はこれ以降、すべて **WSL + Docker Engine直接導入**を前提に進めます。コマンド例はすべてWSLのターミナル上でそのまま実行できる形で記載します。

## 章末チェックリスト

- [ ] Docker DesktopとWSL+Docker Engine直接導入の違いを表で説明できる
- [ ] Docker Desktop環境でリソース制限を変更する画面がどこか説明できる

## 次へ

- [03-docker-core-operations/README.md](../03-docker-core-operations/README.md)
