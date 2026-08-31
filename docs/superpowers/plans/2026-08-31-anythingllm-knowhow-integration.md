# AnythingLLMノウハウ統合 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Windows + WSL2 + Docker Engineで同一ComposeプロジェクトにAnythingLLMとOllamaを配置する、体系的で簡潔な運用ガイドへ正本資料を更新する。

**Architecture:** 正本資料のみを編集し、導入方式はCompose内Ollamaへ一本化する。基礎は既存の章2から章11に統合し、コンテナランタイムの説明は章12の任意学習として隔離する。

**Tech Stack:** Markdown、Mermaid、Docker Compose、AnythingLLM、Ollama、WSL2、NVIDIA Container Toolkit

---

### Task 1: Compose構成へ導入手順を一本化する

**Files:**
- Modify: `AnythingLLM 実践導入・運用について（DeepWikiベース）.md`

- [ ] **Step 1: 既存の外部Ollama前提を確認する**

`OLLAMA_BASE_PATH`、`host.docker.internal`、WSL2またはWindowsホストのIPアドレスを接続先としている箇所を特定する。

- [ ] **Step 2: アーキテクチャをCompose内通信として記載する**

AnythingLLMをRAGオーケストレーター、Ollamaをチャット/Embedding推論の実行基盤と説明し、サービス名による `http://ollama:11434` 接続を図と本文へ反映する。

- [ ] **Step 3: Composeによる起動手順を追加する**

`ollama_data` と `anythingllm_storage` のnamed volume、`anythingllm` のみの `3001:3001` ポート公開、`OLLAMA_BASE_PATH` と `EMBEDDING_BASE_PATH` のサービス名指定を含む `compose.yaml` を記載する。

- [ ] **Step 4: 静的に確認する**

実行: Markdown診断と、追加したYAMLコードブロックの構文確認。

期待結果: Markdown診断エラーがなく、Compose例ではOllamaの `ports` が未指定である。

### Task 2: 日本語RAGのモデル選定とGPU利用を追加する

**Files:**
- Modify: `AnythingLLM 実践導入・運用について（DeepWikiベース）.md`

- [ ] **Step 1: モデル選定の責務を分ける**

Embeddingモデルは検索、チャット用LLMは回答生成を担当することを明記し、`qwen3-embedding:0.6b`、`qwen3`、`gemma3`を検証候補として整理する。

- [ ] **Step 2: 運用ルールを追加する**

モデルの配布元・ライセンス・商用利用条件の確認、`ollama list` を実際のモデル名の正とすること、Embeddingモデル変更後の再Embeddingを記載する。

- [ ] **Step 3: 任意のGPU手順を追加する**

`nvidia-ctk runtime configure --runtime=docker`、Docker再起動、Composeの `deploy.resources.reservations.devices`、`docker compose exec ollama nvidia-smi` による確認を記載する。

- [ ] **Step 4: 静的に確認する**

実行: Markdown診断。

期待結果: Markdown診断エラーがなく、GPUなしで導入できることとGPU設定が任意であることが分離されている。

### Task 3: RAG検証と障害切り分けを補強する

**Files:**
- Modify: `AnythingLLM 実践導入・運用について（DeepWikiベース）.md`

- [ ] **Step 1: 引用を使うRAG検証を追加する**

登録文書にだけ存在する事実を質問にし、`vector-search`、チャット応答、引用表示の順で検証する方法をTuning節へ追加する。

- [ ] **Step 2: `model is required` の切り分けを追加する**

`docker compose ps -a`、`ollama list`、`ollama run`、`anythingllm`からの `/api/tags`、AnythingLLMの全体/Workspaceモデル選択、ログ確認の順を章9へ追加する。

- [ ] **Step 3: 静的に確認する**

実行: Markdown診断。

期待結果: Markdown診断エラーがなく、障害対応がComposeサービス名の経路に一貫している。

### Task 4: コンテナランタイムの発展節を追加し全体を検証する

**Files:**
- Modify: `AnythingLLM 実践導入・運用について（DeepWikiベース）.md`

- [ ] **Step 1: 章12を任意の発展内容として追加する**

`docker`、`dockerd`、`containerd`、`containerd-shim`、`runc`、Linuxカーネルの実行経路と、`nvidia-container-runtime` が `runc` への設定注入を行うラッパーであることを説明する。

- [ ] **Step 2: 既存内容との重複を整理する**

外部OllamaのIP指定、`host.docker.internal`、WindowsまたはWSL2へのOllama個別配置を導入、設定、ネットワーク、オフライン手順から除去またはCompose内構成に置換する。

- [ ] **Step 3: 最終静的検証を行う**

実行: Markdown診断、見出し番号確認、コードフェンス数の一致確認、Mermaidフェンスの一致確認、`host.docker.internal` と外部Ollama接続記述の検索。

期待結果: Markdown診断エラーなし。Compose内の `http://ollama:11434` を唯一のOllama接続先として記載し、シークレットの実値を含まない。