# 04 Docker Desktopライセンスの実務注意点

## 所要時間

約10分

## 学習目標

- Docker Desktopの商用ライセンス条件の概要を理解する
- 本教材がDocker Engine直接導入を選んだ理由を再確認する

---

## Docker Desktopの商用ライセンス条件

Docker Desktopは、個人利用・小規模事業者・教育機関・非商用のオープンソースプロジェクトでは無料で利用できますが、一定規模以上の企業（従業員数や年間売上高が基準になることが一般的）では有償サブスクリプション（Docker Business等）の契約が必要になります。

⚠️ 具体的な金額や適用条件はDocker社の方針変更により随時更新されるため、実務で導入を検討する際は必ず [Docker公式サイト](https://www.docker.com/pricing/) の最新情報を確認してください。本教材はライセンス条件の詳細を保証するものではありません。

## 本教材がDocker Engine直接導入を選んだ理由の再確認

01章で比較した通り、本教材ではDocker Desktopを使わず、WSL内へのDocker Engine直接インストールを主軸にしてきました。これは主に以下の理由によるものでした。

- Docker Engine自体はOSSであり、ライセンス費用が発生しない
- 内部の仕組み（Dockerデーモンの起動、ネットワーク構成など）が見える学習効果がある

## 会社でDocker Desktopが既に導入されている場合

すでにライセンス契約済みでDocker Desktopが会社で導入されている環境であれば、それを使うこと自体には何の問題もありません。02章の [04-docker-desktop-comparison.md](../02-environment-setup/04-docker-desktop-comparison.md) で触れた通り、`docker`コマンド自体は同じであり、本教材で学んだ内容（イメージ・コンテナの操作、ネットワークの仕組み、Compose、トラブルシューティング）はDocker Desktop環境でもそのまま活用できます。異なるのは、リソース設定やWSL統合の操作画面がGUIになっている点だけです。

## 章末チェックリスト

- [ ] Docker Desktopの商用ライセンスが必要になる目安を説明できる
- [ ] 本教材の内容がDocker Desktop環境でもそのまま使えることを理解している

## 次へ

- [09-capstone-project/README.md](../09-capstone-project/README.md)
