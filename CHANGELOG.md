# CHANGELOG

## Unreleased

### Added
- `02-environment-setup/03-vscode-remote-wsl.md` に「WSLから起動した場合とWindowsから直接起動した場合の違い」節を追加（拡張機能の実行場所・既定シェル・ファイルアクセス経路の比較表）
- `07-operations-and-troubleshooting/02-logs-and-monitoring.md` に`docker logs`の基本動作（一括表示で終了する点）・コンテナ名の注意点・主要オプションの比較表を追加
- `03-docker-core-operations/03-volume-basics.md` に`docker volume inspect`の`Mountpoint`フィールドの説明（実体パスの規則・root所有の注意・bind mountとの違い）を追加
- `03-docker-core-operations/04-network-basics-intro.md` に`docker network create`の既定ドライバが`bridge`であることの説明を追加、`04-networking-deep-dive/04-docker-bridge-networking.md`の`docker network inspect`解説に`Driver`フィールドの補足を追加
- `04-networking-deep-dive/04-docker-bridge-networking.md` に「なぜコンテナ名で名前解決できるのか」節を追加（`127.0.0.11`の組み込みDNSサーバーの仕組み、既定bridgeとの違い）
- Mermaid図を体系的に追加（ROADMAPの「ネットワーク構成図追加」を含めて実施）
  - `04-networking-deep-dive/01-container-network-concepts.md`: namespace/bridge/veth pairの関係図
  - `04-networking-deep-dive/04-docker-bridge-networking.md`: 組み込みDNS解決のsequenceDiagram
  - `04-networking-deep-dive/06-network-troubleshooting.md`: パターン1切り分け手順のflowchart
  - `06-compose-multi-service/02-service-discovery-and-network.md`: Composeネットワーク構成図
  - `07-operations-and-troubleshooting/03-common-failures-playbook.md`: ポート競合切り分けのflowchart
  - `09-capstone-project/01-project-brief.md`: 3サービス構成の目標アーキテクチャ図

## v1.0.0 - 2026-08-29

### Added
- Windows + WSL2 + Docker Engine 向け教材の初版を作成
- `docs/` 配下に01〜09章構成の教材本文を追加
- 姉妹教材と共通の補助ファイル（`MASTER-INDEX.md`, `QUICK-REFERENCE.md`, `COMPLETION-REPORT.md`, `PUBLISHING.md`, `VALIDATION_CHECKLIST.md`）を追加
- ebook-build設定を `.github/skills-config/ebook-build/` に追加
