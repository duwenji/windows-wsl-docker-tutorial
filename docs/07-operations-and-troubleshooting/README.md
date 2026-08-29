# 07 Operations and Troubleshooting

## この章で学ぶこと

- `.wslconfig`によるリソース制限とVmmemメモリ肥大化への対処
- コンテナログの確認とリアルタイムモニタリング
- ネットワーク以外の典型的な障害パターンへの対処

## 学習目標

- WSL2/Dockerのリソース使用状況を把握し、制限を設定できる
- ログとモニタリングコマンドを使いこなせる
- ポート競合・ディスク容量不足・コンテナ即終了などの障害に対処できる

## 演習案

1. `.wslconfig`でメモリ制限を設定し、反映を確認する
2. `docker system prune`でディスク使用量を整理する
3. わざとポート競合を発生させ、Windows側・WSL側両方で原因プロセスを特定する

## 成功判定

- リソース管理・ログ確認・障害対応の一連の流れを自力で実施できる
- ネットワーク起因の障害と、それ以外の障害を切り分けられる

## 次章へ

- [08-security-and-enterprise-network/README.md](../08-security-and-enterprise-network/README.md)
