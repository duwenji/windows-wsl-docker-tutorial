# 02 Environment Setup

## この章で学ぶこと

- `wsl --install` によるWSL2環境の構築
- WSL内へのDocker Engine直接インストール手順
- VS Code Remote-WSL / Dev Containers拡張のセットアップ
- Docker Desktopとの違い（比較コラム）

## 学習目標

- WSL2 + Docker Engineの開発環境を自力で構築できる
- `docker run hello-world` が成功する状態を作れる
- Docker Desktop環境に配属された場合でも設定箇所を読み替えられる

## 演習案

1. Docker Engineをインストールし `docker run hello-world` が成功することを確認する
2. VS CodeのRemote-WSL拡張でWSL内フォルダを開けることを確認する
3. Docker DesktopとWSL+Docker Engine直接導入の比較表を自分の言葉で説明する

## 成功判定

- `sudo`なしで`docker`コマンドが実行でき、WSL再起動後もDockerデーモンが自動起動する
- Docker Desktop環境との違いを人に説明できる

## 次章へ

- [03-docker-core-operations/README.md](../03-docker-core-operations/README.md)
