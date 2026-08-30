# CHANGELOG

## Unreleased

### Added
- `05-dev-workflow-and-file-sharing/03-vscode-dev-containers.md` に「Reopen in Containerで内部的に何が起きるか」節を追加：起動処理の流れ（devcontainer.json読み込み→イメージpull→コンテナ起動→コンテナ内VS Codeサーバーのインストール→拡張機能再インストール→接続切り替え）をsequenceDiagramで、実行前後の構成変化（WSL側サーバー接続→コンテナ内サーバー接続への切り替わり、VS Codeサーバーが二重にネストする点）をflowchartで可視化。章末チェックリストに関連項目を1件追加
- `02-environment-setup/03-vscode-remote-wsl.md` にmermaid図を追加：Remote-WSL拡張によるUI（Windows側）とVS Codeサーバー（WSL側）のクライアント/サーバー分離構造を可視化
- `05-dev-workflow-and-file-sharing/03-vscode-dev-containers.md` にmermaid図を追加：Dev Containers起動時、直前に開いていたパス（WSL側 vs Windows側）によってbind mount元と性能が分岐する様子をflowchartで可視化
- `10-local-rag-anythingllm-ollama/01-architecture-and-compose-setup.md` に「`anythingllm`のHEALTHCHECKは実際に何をチェックしているか」節を追加：`mintplexlabs/anythingllm`イメージのHEALTHCHECK命令（`--interval=1m --timeout=10s --start-period=1m`）と`docker-healthcheck.sh`が`/api/ping`へHTTPチェックしている実装を解説。章末チェックリストに関連項目を1件追加
- `02-environment-setup/02-install-docker-engine.md` の動作確認節に「実行後の後始末は必要か」を追加：`docker run`が終了済みコンテナを既定で残す理由（失敗時の`docker logs`調査のため）、`docker rm`/`docker container prune`/`--rm`の使い分けを解説。章末チェックリストに関連項目を1件追加
- `02-environment-setup/02-install-docker-engine.md` に「なぜ`apt install docker.io`ではなくこの手順なのか」節と、インストールコマンド各行の役割解説表を追加（GPG鍵の`dearmor`/`signed-by`の意味、アーキテクチャ・コードネームを動的取得する理由を含む）。章末チェックリストに関連項目を2件追加
- `02-environment-setup/01-install-wsl.md` に「初回セットアップ後、なぜ2回目以降は何も聞かれないのか」節を追加：`/etc/wsl.conf`の`[user] default=`によるユーザー名の永続化、`wsl`起動が認証を伴わない単なるプロセス起動である理由、Windowsサインインが信頼境界として機能する仕組みを解説。章末チェックリストに関連項目を2件追加
- `10-local-rag-anythingllm-ollama/04-container-runtime-internals.md`（発展）を追加：`dockerd`/`containerd`/`runc`の三層構造、`runc`が行うLinux syscallレベルの処理（namespaces/cgroups/pivot_root）、`nvidia-container-runtime`が`runc`をラップするシムである仕組みをmermaid図付きで解説。`01-architecture-and-compose-setup.md`のGPU passthrough節から参照リンクを追加、`03-rag-workspace-and-verification.md`の「次へ」を接続
- 新章`10-local-rag-anythingllm-ollama`を追加（AnythingLLM + OllamaによるローカルRAG環境構築）
  - `01-architecture-and-compose-setup.md`: 全体アーキテクチャ、docker-compose構成、WSL2でのGPU passthrough設定
  - `02-selecting-japanese-models.md`: 日本語対応Embedding/チャットLLMモデルの選定表（qwen3-embedding, qwen3, Swallow, ELYZA等）
  - `03-rag-workspace-and-verification.md`: ワークスペース作成、ドキュメント取り込み、RAG動作検証、チャンクサイズ等の注意点
  - `README.md`, `MASTER-INDEX.md`, `QUICK-REFERENCE.md`, `docs/index.md`, `ROADMAP.md`を10章追加に合わせて更新、09章capstoneの「次へ」リンクを10章に接続
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
- `04-networking-deep-dive/03-wsl2-network-modes.md` に、localhostフォワーディングがWindows側とWSL側でポート番号の名前空間を早い者勝ちで奪い合う仕組みの説明を追加。`07-operations-and-troubleshooting/03-common-failures-playbook.md`のポート競合説明にもこの原因への参照を追加
- `04-networking-deep-dive/03-wsl2-network-modes.md` に「なぜNATモードは企業VPN環境で問題を起こすのか」節を追加（デフォルトルートの奪い合いの具体例、VPNフィルタドライバによる遮断、経路図）
- 同ファイルに、mirrored mode側の経路図を追加（NATモード図と対比し、中間ステップが無くなる構造を可視化）
- `04-networking-deep-dive/04-docker-bridge-networking.md` に、`br-`インターフェースが「コンテナ1つ」ではなく「ユーザー定義ネットワーク1つ」に対応することの説明を追加（命名規則`br-<ネットワークID先頭12桁>`、確認コマンド）
- `08-security-and-enterprise-network/01-proxy-and-certificates.md` に、`/etc/environment`だけではaptにプロキシが効かない理由（sudoのenv_reset）の説明を追加
- 同ファイルにMermaid図を2件追加: 4つのプロキシ設定（環境変数/apt/dockerd/ビルド時）が独立している全体像、WSL側とコンテナ側のCA証明書ストアが別物であることの図

### Fixed
- `10-local-rag-anythingllm-ollama/01-architecture-and-compose-setup.md` の`docker-compose.yml`に、チャット用LLMモデルを指定する`OLLAMA_MODEL_PREF=qwen3:8b`が抜けていたため追加（Embedding側の`EMBEDDING_MODEL_PREF`は設定済みだったが、対応するチャット側の設定が欠落していた）。解説文・設定項目表・章末チェックリストも合わせて更新
- `10-local-rag-anythingllm-ollama/02-selecting-japanese-models.md` の「`bge-m3`はOllama公式ライブラリには存在しない」という記述を訂正：現在は`bge-m3:567m`として公式ライブラリに掲載されているため、Embeddingモデル表に追記した上で説明文を修正

## v1.0.0 - 2026-08-29

### Added
- Windows + WSL2 + Docker Engine 向け教材の初版を作成
- `docs/` 配下に01〜09章構成の教材本文を追加
- 姉妹教材と共通の補助ファイル（`MASTER-INDEX.md`, `QUICK-REFERENCE.md`, `COMPLETION-REPORT.md`, `PUBLISHING.md`, `VALIDATION_CHECKLIST.md`）を追加
- ebook-build設定を `.github/skills-config/ebook-build/` に追加
