# 01 WSL and Docker Fundamentals

## この章で学ぶこと

- Dockerの基本概念（コンテナ＝プロセス隔離であり仮想マシンではないこと）
- DockerがLinuxカーネルの機能に依存していること
- WSL1とWSL2の決定的な違い、WSL2がHyper-Vベースの軽量VMであること
- イメージ/コンテナ/レジストリ/デーモンといったDockerの基本語彙

## 学習目標

- なぜWindowsでDockerを使うのにWSL2が必要なのかを、Linuxカーネル機能の観点から説明できる
- Docker Desktop / WSL+Docker Engine / Hyper-V VM の3方式の違いを説明できる
- `wsl -l -v` でWSLのバージョンを確認できる

## 演習案

1. Docker Desktop・WSL+Docker Engine・Hyper-V VMの3方式を比較した表を、自分の言葉で作り直してみる
2. `wsl --status` と `wsl -l -v` を実行し、自分の環境のディストロとバージョンを確認する

## 成功判定

- 「コンテナは軽量な仮想マシンである」という誤解を持たずに説明できる
- Dockerを動かすにはWSL2が必須である理由を、cgroups/namespacesという語を使って説明できる

## 次章へ

- [02-environment-setup/README.md](../02-environment-setup/README.md)
