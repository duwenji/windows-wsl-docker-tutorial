# 02 WSLのアーキテクチャ

## 所要時間

約15分

## 学習目標

- WSL1とWSL2の決定的な違いを説明できる
- WSL2の実体がHyper-Vベースの軽量VMであることを理解する
- `wsl --status` / `wsl -l -v` で現在の状態を確認できる

---

## WSL1とWSL2の違い

| 項目 | WSL1 | WSL2 |
|---|---|---|
| 実現方式 | Linuxシステムコールをwindowsカーネルが変換するトランスレーション層 | Hyper-V上で動く軽量VM上に本物のLinuxカーネルを搭載 |
| Linuxカーネル | なし（Windowsカーネルが代行） | あり（本物のLinuxカーネル） |
| ファイルI/O速度（Linux側） | 速い | WSL1よりは劣るが実用的な速度 |
| Dockerの動作 | ❌ 動かない（カーネル機能が無いため） | ✅ 動く |

⚠️ 01章で説明した通り、DockerはLinuxカーネルのnamespaces/cgroupsに依存しています。WSL1はLinuxカーネルそのものを持たないため、**Dockerを動かすには必ずWSL2を使う必要があります**。

## WSL2の実体: Hyper-Vベースの軽量ユーティリティVM

WSL2は、Hyper-Vの技術を使って作られた「軽量ユーティリティVM」の中で、実際のLinuxカーネルを起動しています。通常の仮想マシンのような重厚な仮想化ではなく、高速起動・低メモリオーバーヘッドに最適化された専用のVMです。

このVMは、Windowsのタスクマネージャーから `Vmmem`（または `Vmmem WSL`）というプロセス名で確認できます。これは07章のリソース管理の章で再び登場します。

### 現在の状態を確認する

```bash
# PowerShell側で実行
wsl --status
```

```
既定のディストリビューション: Ubuntu
既定のバージョン: 2
```

```bash
# ディストロごとのバージョンを確認
wsl -l -v
```

```
  NAME      STATE           VERSION
* Ubuntu    Running         2
```

`VERSION`列が`2`になっていることが、Docker Engineを動かすための前提条件です。

## ディストリビューションとWSL2 VMの関係

現行のWSLアーキテクチャでは、Ubuntu・Debianなど複数のディストロをインストールしても、それらは共通の1つのWSL2軽量VM（Linuxカーネル）の上で動作します。ディストロごとに完全に独立したカーネルを持つわけではありません。

この「共通のVMの上に各ディストロが乗っている」という構造は、後述するネットワークやファイルシステムの共有範囲に影響します。詳しい仕組みは [04-networking-deep-dive](../04-networking-deep-dive/README.md) で扱います。

## 演習

1. PowerShellで `wsl --status` と `wsl -l -v` を実行し、既定のディストロとバージョンを確認する
2. タスクマネージャーの「詳細」タブで `Vmmem` プロセスを探し、CPU/メモリ使用量を確認する

## 章末チェックリスト

- [ ] WSL1とWSL2の違いを、Linuxカーネルの有無で説明できる
- [ ] Dockerを動かすにはWSL2が必須である理由を説明できる
- [ ] `wsl -l -v` でディストロのバージョンを確認できた

## 次へ

- [03-docker-concepts.md](./03-docker-concepts.md)
