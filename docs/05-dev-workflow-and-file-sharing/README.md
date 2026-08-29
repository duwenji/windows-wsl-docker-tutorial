# 05 Dev Workflow and File Sharing

## この章で学ぶこと

- WSL側とWindows側、2つのファイルシステムの性能差
- bind mountとnamed volumeの性能特性の違い
- VS Code Dev Containersでの開発ループ
- 改行コード問題への対処

## 学習目標

- プロジェクトをWSL側ファイルシステムに置くべき理由を説明できる
- 性能を意識したbind mount / named volumeの使い分けができる
- Dev Containersで高速な開発ループを構築できる

## 演習案

1. WSL側とWindows側でファイルI/Oの速度を実測比較する
2. `devcontainer.json`を作成し、Remote-WSL経由でDev Containersを起動する
3. CRLF混入トラブルを再現し、`.gitattributes`で対処する

## 成功判定

- 「なぜプロジェクトをWSL側に置くべきか」を9pプロトコルの観点から説明できる
- Dev Containersを使った開発ループを自力で構築できる

## 次章へ

- [06-compose-multi-service/README.md](../06-compose-multi-service/README.md)
