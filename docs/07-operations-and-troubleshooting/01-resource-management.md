# 01 リソース管理

## 所要時間

約20分

## 学習目標

- Vmmemのメモリ肥大化への対処法を実践できる
- Dockerのディスク使用量を管理できる

---

## Vmmemプロセスとメモリ肥大化

01章で触れた`Vmmem`（WSL2 VM全体を表すプロセス）は、負荷の高い処理を行った後もメモリを解放せずに保持し続けることがあります。長時間WSLを使っていると、タスクマネージャー上で`Vmmem`のメモリ使用量がどんどん増えているように見える現象です。

## .wslconfigでのリソース制限

02章で最低限の設定は済ませていますが、改めて`%UserProfile%\.wslconfig`の設定項目を確認します。

```ini
[wsl2]
memory=4GB
processors=2
swap=2GB
```

- `memory`: WSL2 VM全体が使える最大メモリ量
- `processors`: WSL2 VMに割り当てるCPUコア数
- `swap`: スワップ領域のサイズ（既定は物理メモリと同量）

## 設定の反映

`.wslconfig`の変更は、WSLを完全にシャットダウンしないと反映されません。

```powershell
wsl --shutdown
```

その後、再度WSLターミナルを開けば新しい設定が有効になります。

## メモリ解放のためのコマンド

即座にメモリを解放したい場合は、`wsl --shutdown`が最も確実です（起動中の全プロセス・コンテナも停止するので注意してください）。

```powershell
wsl --shutdown
```

WSLを終了させたくない場合、Linuxカーネルのキャッシュだけを解放する方法もありますが、根本的な解決にはならないことが多いです。

```bash
# 効果は限定的
sudo sh -c "echo 1 > /proc/sys/vm/drop_caches"
```

## Dockerのディスク使用量管理

コンテナ・イメージ・ボリュームは、使わなくなった後もディスクに残り続けます。

```bash
# 使用量の内訳を確認
docker system df
```

```
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          12        3         2.1GB     1.4GB (66%)
Containers      5         2         340MB     120MB (35%)
Local Volumes   4         2         890MB     210MB (23%)
```

```bash
# 停止中コンテナ・未使用ネットワーク・ダングリングイメージをまとめて削除
docker system prune

# 未使用のボリュームも含めて削除（データ消失に注意）
docker volume prune
```

⚠️ `docker system prune -a`は、**使用中でないすべてのイメージ**（タグ付きイメージも含む）を削除します。次回ビルド時に再ダウンロードが必要になるため、実行前に`docker system df`で影響範囲を必ず確認してください。

## 演習

1. `docker system df`で現在のディスク使用量を確認する
2. `.wslconfig`の`memory`値を変更し、`wsl --shutdown`後に設定が反映されることを確認する（WSL内で`free -h`を実行し、認識されているメモリ量を確認する）
3. `docker system prune`を実行し、削除前後で`docker system df`の値がどう変わるか確認する

## 章末チェックリスト

- [ ] `.wslconfig`でメモリ・CPU制限を設定できた
- [ ] `docker system df`でディスク使用量の内訳を確認できた
- [ ] `docker system prune`と`-a`オプションの違いを説明できる

## 次へ

- [02-logs-and-monitoring.md](./02-logs-and-monitoring.md)
