# Windows WSL Docker Tutorial — Design

## Goal

Windows上でWSL2とDocker Engineを使って開発する日本語教材を、既存の姉妹教材（kubernetes-tutorial, css-tutorial等）と同じ構成規約で新規作成する。

## Audience / Scope

- WSL、Dockerともに未経験の読者を対象とするが、実務での利用（実際の開発フローに組み込む）を前提とした深さまで踏み込む。
- 抽象的な概念説明で終わらせず、手を動かして確認できる手順を優先する（既存教材と同じ方針）。

## Environment decisions

- **主軸は WSL 内への Docker Engine 直接インストール**（`apt install docker-ce` 系）。Docker Desktopは使わない。
  - 理由: 内部の仕組みが見える学習効果、企業ライセンス費用の回避。
- Docker Desktop は 02 章内で「比較コラム」として簡潔に触れるのみ（実務でDocker Desktop環境に配属された場合に差分がわかる程度）。
- エディタは VS Code + Remote-WSL + Dev Containers 拡張を使う。

## Chapter structure (9 chapters + capstone)

| # | ディレクトリ | 内容 |
|---|---|---|
| 01 | `01-wsl-and-docker-fundamentals` | WSLとは、WSL1/2の違い、Dockerの基本概念、なぜWSL+Dockerを組み合わせるか（全体アーキテクチャ） |
| 02 | `02-environment-setup` | `wsl --install`、ディストロ選択、WSL内へのDocker Engine直接導入、VS Code Remote-WSL/Dev Containers、Docker Desktop比較コラム |
| 03 | `03-docker-core-operations` | image/container/volume/networkの基本操作をWSL上で実践 |
| 04 | `04-networking-deep-dive` | **ネットワークの深掘り章（新設）**。概念、Windows⇔WSL2 VM⇔Docker Engine⇔コンテナの層構造、WSL2のネットワークモード（NAT/mirrored）、Dockerブリッジネットワーク、`-p`公開の実経路、トラブルシュート |
| 05 | `05-dev-workflow-and-file-sharing` | Windows/WSLファイルシステム境界、bind mount vs named volumeの性能差、VS Code Dev Containersでの開発ループ、改行コード注意点 |
| 06 | `06-compose-multi-service` | Docker Composeで複数サービス連携（04章でネットワーク基礎を終えているためCompose特有の話に集中） |
| 07 | `07-operations-and-troubleshooting` | リソース管理（vmmemと`.wslconfig`）、ログ確認、一般的な障害対応（ネットワーク以外） |
| 08 | `08-security-and-enterprise-network` | 企業プロキシ/証明書対応、rootless Docker、Secret管理、Docker Desktopライセンスの実務注意点 |
| 09 | `09-capstone-project` | 総合演習：Webアプリ+DBをWSL2+Docker Composeで構築し、Dev Containersで開発ループを回す |

### 04章（ネットワーク深掘り）の詳細構成

1. 概念編: コンテナネットワークとは何か／Linuxのnetwork namespaceとbridgeの基礎
2. 層構造の理解: Windows ホスト ⇔ WSL2 VM（Hyper-V軽量VM、独自Linuxカーネル）⇔ WSL2内のDocker Engine ⇔ コンテナ、の4層構造を図解
3. WSL2自体のネットワーク方式: NATモード（既定）と mirrored networking mode（Windows 11 22H2+）の違い、`vEthernet (WSL)`アダプタ、`.wslconfig`の`networkingMode`
4. Docker側のネットワーク: `docker0`ブリッジ、ユーザー定義bridge、コンテナへのIP割当、`docker network inspect`
5. `-p`（ポート公開）の実経路: コンテナ→dockerブリッジ(iptables NAT)→WSL2 VM→Windowsホストのlocalhostフォワーディング→外部
6. 実践トラブルシュート: 典型的な疎通不可パターンの切り分け手順

## Scaffolding (既存教材と同水準)

- ルート: `README.md`, `MASTER-INDEX.md`, `QUICK-REFERENCE.md`, `CHANGELOG.md`, `CONTRIBUTING.md`, `LICENSE`, `ROADMAP.md`, `PUBLISHING.md`, `VALIDATION_CHECKLIST.md`, `COMPLETION-REPORT.md`, `.gitattributes`
- `.github/copilot-instructions.md`（kubernetes-tutorialと同形式、WSL/Docker向けに書き換え）
- `.github/ISSUE_TEMPLATE/{bug_report.yml, feature_request.yml, config.yml}`
- `.github/pull_request_template.md`
- `.github/skills-config/ebook-build/{windows-wsl-docker-tutorial.build.json, .metadata.yaml, .kdp.yaml, invoke-build.ps1}`
- `docs/00-COVER.md`, `docs/index.md`, `docs/_config.yml`
- 各章 `docs/NN-xxx/README.md` + レッスンファイル（`01-xxx.md`形式）

## Ebook / Quiz integration

- ebook-build を他教材と同水準で組み込む（EPUB/PDF/KDP markdown）。
- クイズ連携は既存 `spa-quiz-app` への誘導リンクのみ（標準コールアウト文言をREADME/00-COVERに設置）。新規クイズ問題の追加は本タスクのスコープ外。

## Out of scope

- spa-quiz-app への新規クイズ問題追加
- Docker Desktop環境でのハンズオン手順（比較コラムのみ）
- クラウド上のマネージドコンテナサービス（ACI, ECS等）
