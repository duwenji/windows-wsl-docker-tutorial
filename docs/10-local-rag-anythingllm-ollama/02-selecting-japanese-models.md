# 02 日本語対応モデルの選び方

## 所要時間

約20分

## 学習目標

- RAGにおけるEmbeddingモデルとチャット用LLMモデルの役割の違いを説明できる
- ハードウェア（VRAM）に応じて日本語対応モデルを選定できる
- コミュニティ公開モデルを使う際の注意点を理解する

---

## Embeddingモデルとチャット用LLMモデルの違い

RAGでは2種類のモデルを使い分けます。

- **Embeddingモデル**: ドキュメントとユーザーの質問を数値ベクトルに変換し、意味的な類似度検索に使う。ここが弱いと、そもそも関連ドキュメントが検索結果に出てこず、LLMがどれだけ優秀でも意味がない
- **チャット用LLMモデル**: 検索で見つかった関連ドキュメントを踏まえて、実際に回答文を生成する

日本語RAGでは、後者（LLM）の日本語力だけに注目しがちですが、**前者（Embedding）の日本語対応が弱いと検索の時点で失敗する**ため、両方を意識して選ぶ必要があります。

## Embeddingモデルの選定

モデルの大きさには、モデル規模を表す「パラメータ数」と、実際に取得する「Ollama配布サイズ」があります。配布サイズはディスク容量の目安であり、実行時に必要なRAM/VRAMはコンテキスト長や同時実行数でも変わります。

| モデル | Ollamaタグ | パラメータ数 | Ollama配布サイズ | 特徴 |
| --- | --- | ---: | ---: | --- |
| **Qwen3 Embedding**（推奨） | `qwen3-embedding:0.6b` | 0.6B | 639MB | 多言語（日本語を含む）の軽量な初期候補 |
| Qwen3 Embedding | `qwen3-embedding:4b` | 4B | 2.5GB | 検索精度を比較する中間候補 |
| Qwen3 Embedding | `qwen3-embedding:8b` | 8B | 4.7GB | 精度重視の候補 |
| EmbeddingGemma | `embeddinggemma:300m` | 300M | 622MB | Google製の軽量・多言語モデル |
| Nomic Embed Text v2 MoE | `nomic-embed-text-v2-moe` | 総数475M、推論時305M | 958MB | 多言語MoEモデル。最大入力は512トークン |
| BGE-M3 | `bge-m3:567m` | 567M | 1.2GB | 100以上の言語と8Kコンテキストに対応 |

`bge-m3`はOllama公式ライブラリにも`bge-m3:567m`として掲載されており、多言語RAG向けの選択肢として利用できます。ただし本教材では、Apache-2.0ライセンスでMTEB上位かつ日本語対応が明記されている**`qwen3-embedding`を第一候補**とするのが無難です。

```bash
docker compose exec ollama ollama pull qwen3-embedding:0.6b
```

## チャット用LLMモデルの選定

次はOllama公式ライブラリで確認できる主なタグです。配布サイズだけで必要VRAMを断定せず、まず小さなモデルでチャット応答時間と日本語の回答品質を測定します。

| モデル | Ollamaタグ | パラメータ数 | Ollama配布サイズ | 用途の目安 |
| --- | --- | ---: | ---: | --- |
| Gemma 3 | `gemma3:4b` | 4B | 3.3GB | 低リソース環境での初期検証 |
| Qwen 3 | `qwen3:0.6b` | 0.6B | 523MB | 最小構成の動作確認 |
| Qwen 3 | `qwen3:4b` | 4B | 2.5GB | 軽量な比較候補 |
| **Qwen 3**（推奨） | `qwen3:8b` | 8B | 5.2GB | 日本語RAGの標準的な比較候補 |
| Qwen 3 | `qwen3:14b` | 14B | 9.3GB | 品質を優先する比較候補 |
| Gemma 3 | `gemma3:12b` | 12B | 8.1GB | 品質を優先する比較候補 |
| Gemma 3 | `gemma3:27b` | 27B | 17GB | 十分な計算資源がある場合の比較候補 |
| Gemma 4 | `gemma4:e2b` | 実効2.3B（埋め込みを含めて5.1B） | 7.2GB | エッジ向け、128Kコンテキスト |
| Gemma 4 | `gemma4:e4b` | 実効4.5B（埋め込みを含めて8B） | 9.6GB | エッジ向け、128Kコンテキスト |
| Gemma 4 | `gemma4:12b` | 12B | 7.6GB | ワークステーション向け、256Kコンテキスト |
| Gemma 4 | `gemma4:26b` | 25.2B MoE（推論時3.8B有効） | 19GB | ワークステーション向け、256Kコンテキスト |
| Gemma 4 | `gemma4:31b` | 30.7B Dense | 20GB | ワークステーション向け、256Kコンテキスト |

```bash
docker compose exec ollama ollama pull qwen3:8b
```

⚠️ コミュニティ名前空間で公開される日本語特化モデルを使う場合は、配布元のREADMEでライセンス条件、商用利用可否、ベースモデルの出所を確認してください。まずは公式ライブラリの`qwen3`または`gemma3`で比較基準を作ると判断しやすくなります。

Gemma 4の `E2B` と `E4B` の `E` はeffective parameters（実効パラメータ数）を表します。配布サイズとパラメータ数は単純比例しないため、必要なRAM/VRAMは実際のコンテキスト長と同時実行数を含めて検証してください。

## AnythingLLMへのモデル設定

pullしたモデルは、AnythingLLMの管理画面（`http://localhost:3001` → 設定 → LLM Preference / Embedding Preference）から選択します。`01-architecture-and-compose-setup.md`の`docker-compose.yml`で設定した環境変数は初期値として使われるだけなので、pull後にUI側でモデル名を選び直す必要があります。

- LLM Preference: Ollama → `qwen3:8b`（pullしたモデル名と一致させる）
- Embedding Preference: Ollama → `qwen3-embedding:0.6b`

## 演習

1. `qwen3-embedding:0.6b`と、自分のハードウェアに合ったチャット用モデルをpullする
2. AnythingLLMの管理画面で、pullしたモデルをLLM Preference / Embedding Preferenceに設定する

## 章末チェックリスト

- [ ] EmbeddingモデルとチャットLLMモデルの役割の違いを説明できる
- [ ] 自分のVRAMに合ったモデルを選べる
- [ ] コミュニティ名前空間で公開されたモデルを使う際に確認すべき点を説明できる

## 次へ

- [03-rag-workspace-and-verification.md](./03-rag-workspace-and-verification.md)
