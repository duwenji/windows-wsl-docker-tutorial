# AnythingLLMノウハウ統合設計

## 目的

`AnythingLLM 実践導入・運用について（DeepWikiベース）.md` を、Windows + WSL2 + Docker Engine環境でAnythingLLMを導入・運用するための体系的で簡潔な正本資料にする。`10-local-rag-anythingllm-ollama` の実践ノウハウは、同資料の既存章へ統合し、内容の重複を作らない。

## 対象としないこと

- チュートリアル第10章の削除または書き換え
- Docker Desktopを前提とする構成への変更
- 特定モデルの品質保証または固定推奨
- 実環境のコンテナ起動、モデル取得、APIキーの発行

## 統合方針

### 正本と章の責務

- 正本は `AnythingLLM 実践導入・運用について（DeepWikiベース）.md` とする。
- AnythingLLMとOllamaは同一のComposeプロジェクトで起動する構成だけを扱う。
- 導入に必須の内容と、コンテナランタイム内部の発展知識を分ける。
- 検証は「応答が返る」だけで終えず、引用または検索結果により登録文書を参照していることを確認する。

### 章別の変更

| 章 | 統合内容 |
| --- | --- |
| 2. 仕組み | AnythingLLMをRAGオーケストレーター、OllamaをチャットおよびEmbedding推論の実行基盤として説明する。Compose内では `ollama` サービス名を接続先として使う。 |
| 3. インストール | AnythingLLMとOllamaを同一Composeプロジェクトで起動する例へ一本化する。モデルとアプリ保存領域を個別のnamed volumeで永続化する。 |
| 3.1.2 ハードウェア | GPU利用時のNVIDIA Container Toolkit設定、ComposeのGPU予約、確認方法とWSL2のGPU公開制約を追加する。 |
| 4. 設定方法 | Embeddingモデルとチャット用LLMの役割、選定基準、Ollamaモデル設定例、モデル変更後に必要な再Embeddingを整理する。 |
| 4.8 Tuning | 文書固有の質問と引用表示によるRAG検証を追加する。検索品質、回答品質、性能を区別して評価する。 |
| 9. つまずきやすいポイント | `model is required` の原因と、コンテナ状態、モデル取得、Ollama単体推論、サービス間HTTP疎通、ログ確認の順で行う切り分けを追加する。 |
| 12. 発展 | `dockerd`、`containerd`、`runc`、NVIDIAコンテナランタイムの関係を、GPU passthroughの背景知識として任意学習用に記載する。 |

## 構成別の接続規則

### 推奨: Compose内のOllama

- `anythingllm` と `ollama` を同一のComposeプロジェクトで起動する。
- `OLLAMA_BASE_PATH` と `EMBEDDING_BASE_PATH` は `http://ollama:11434` とする。
- `ollama` は `ports` を指定せず、Composeネットワーク内でのみ公開する。
- Windowsのブラウザに公開するのは `anythingllm` の `3001` 番ポートだけとする。

## モデル選定と運用ルール

- 日本語RAGは、チャット用LLMだけでなくEmbeddingモデルの日本語・多言語性能を評価する。
- 初期候補は軽量な `qwen3-embedding:0.6b` と、ハードウェア条件に応じた `qwen3` または `gemma3` 系とする。利用可能なモデル名は `ollama list` を正とする。
- モデルのライセンス、配布元、商用利用条件は導入前に確認する。特にコミュニティ名前空間のモデルは公式配布物として扱わない。
- Embeddingモデル、チャンクサイズ、チャンク重複を変更した場合は、既存文書を再Embeddingする。

## 検証と障害対応

### RAG検証

1. 登録文書にだけ含まれる事実を質問として用意する。
2. `vector-search` で関連チャンクが返ることを確認する。
3. チャット応答と引用表示で、登録文書が参照されていることを確認する。
4. 同じ質問セットで変更前後の検索品質、根拠一致率、応答時間、リソース使用量を比較する。

### `model is required` の切り分け

1. `docker compose ps -a` でOllamaが起動していることを確認する。
2. `docker compose exec ollama ollama list` でチャット用モデルが取得済みか確認する。
3. `docker compose exec ollama ollama run <モデル名> "test"` でOllama単体推論を確認する。
4. `docker compose exec anythingllm curl -s http://ollama:11434/api/tags` でサービス間通信を確認する。
5. AnythingLLMの全体設定とWorkspace個別設定で、チャット用モデルが選択されていることを確認する。
6. `docker compose logs` で両サービスの直近エラーを確認する。

## 発展節の範囲

発展節では、Docker CLIからLinuxカーネルまでの実行経路を `docker` -> `dockerd` -> `containerd` -> `containerd-shim` -> `runc` として説明する。GPUを使う場合、`nvidia-container-runtime` は `runc` を置き換えるのではなく、GPUデバイスとドライバ設定を注入してから `runc` に引き渡すラッパーとして扱う。

## 検証方法

- Markdown見出しの階層、コードフェンス、Mermaidフェンス、表の区切りを静的に確認する。
- 既存の章番号と参照番号が整合しているか確認する。
- Compose例のYAML構造を確認する。
- 文書内の機密値を示すプレースホルダー以外のAPIキーが含まれないことを確認する。