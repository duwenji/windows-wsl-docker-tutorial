# 02 ログとモニタリング

## 所要時間

約15分

## 学習目標

- コンテナログの基本操作ができる
- `docker stats`でリソース使用状況を監視できる
- ログドライバの既定挙動と注意点を説明できる

---

## コンテナログの基本

```bash
# 全ログを表示
docker logs web

# 直近の指定時刻以降のログ
docker logs --since 10m web

# リアルタイムで末尾100行から追跡
docker logs -f --tail 100 web
```

## Composeでの複数サービスログ

```bash
# 全サービスのログをまとめて表示
docker compose logs -f

# 特定サービスのみ
docker compose logs -f web db
```

## リソース使用状況の確認

```bash
docker stats
```

```
CONTAINER ID   NAME   CPU %   MEM USAGE / LIMIT   NET I/O           BLOCK I/O
a1b2c3d4e5f6   web    0.15%   12.5MiB / 4GiB      1.2kB / 0B        0B / 0B
```

CPU使用率、メモリ使用量、ネットワークI/O、ディスクI/Oをリアルタイムで確認できます。`Ctrl+C`で終了します。

## ログドライバの既定と注意点

Dockerの既定のログドライバは`json-file`で、コンテナの標準出力・標準エラー出力をJSON形式のファイルとしてホスト側に保存します。ログをローテーションする設定をしていない場合、**ログファイルが無制限に肥大化し、ディスクを圧迫する**ことがあります。

```bash
docker inspect web --format '{{.HostConfig.LogConfig}}'
```

```
{json-file map[]}
```

`map[]`の部分が空の場合、サイズ上限が設定されていません。実務では、コンテナ起動時に上限を明示することが推奨されます。

```bash
docker run -d --name web \
  --log-opt max-size=10m --log-opt max-file=3 \
  -p 8080:80 nginx
```

## 演習

1. `docker logs -f`でコンテナのログをリアルタイムに追跡する
2. `docker stats`でリソース使用状況を確認する
3. `--log-opt max-size`を指定してコンテナを起動し、`docker inspect`でログ設定が反映されていることを確認する

## 章末チェックリスト

- [ ] `docker logs`と`docker compose logs`を使い分けられる
- [ ] `docker stats`でリソース状況を確認できた
- [ ] ログドライバのサイズ上限を設定する理由を説明できる

## 次へ

- [03-common-failures-playbook.md](./03-common-failures-playbook.md)
