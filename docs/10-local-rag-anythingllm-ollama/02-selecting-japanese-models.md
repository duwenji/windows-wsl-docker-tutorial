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

| モデル                            | Ollamaタグ                                   | サイズ   | 特徴                                                                                                |
| --------------------------------- | -------------------------------------------- | -------- | --------------------------------------------------------------------------------------------------- |
| **qwen3-embedding**（推奨） | `qwen3-embedding:0.6b` / `:4b` / `:8b` | 0.6B〜8B | Ollama公式ライブラリ、多言語（日本語含む）、Apache-2.0、MTEB上位。0.6bはメモリ節約、4b/8bは精度重視 |
| embeddinggemma                    | `embeddinggemma`                           | 300M     | Google製、軽量・多言語、低スペック環境向け                                                          |
| nomic-embed-text-v2-moe           | `nomic-embed-text-v2-moe`                  | -        | 多言語対応、Ollamaで人気のシリーズ                                                                  |
| bge-m3                            | `bge-m3:567m`                               | 567M     | BAAI製、Ollama公式ライブラリに掲載。密集/多ベクトル/スパース検索を同時実行でき、100以上の言語・8Kコンテキストに対応                |

`bge-m3`はOllama公式ライブラリにも`bge-m3:567m`として掲載されており、多言語RAG向けの選択肢として利用できます。ただし本教材では、Apache-2.0ライセンスでMTEB上位かつ日本語対応が明記されている**`qwen3-embedding`を第一候補**とするのが無難です。

```bash
docker compose exec ollama ollama pull qwen3-embedding:0.6b
```

## チャット用LLMモデルの選定（VRAM別）

| ハードウェア想定           | モデル                        | Ollamaタグ                                       | 備考                                                                            |
| -------------------------- | ----------------------------- | ------------------------------------------------ | ------------------------------------------------------------------------------- |
| GPUなし/8GB前後            | Gemma 3                       | `gemma3:4b`                                    | 軽量・高速、検証用途に十分                                                      |
| モバイル/エッジ相当        | Gemma 4（新）                 | `gemma4:e2b` / `:e4b`                        | Gemini 3ベースの最新世代。140以上の言語に対応とされるが、日本語での精度は要検証 |
| 8〜16GB VRAM               | **Qwen 3**（推奨）      | `qwen3:8b`                                     | ローカル日本語性能で高い評価                                                    |
| 16GB以上 VRAM              | Qwen 3                        | `qwen3:14b`                                    | ビジネス文書作成・要約で実用的な精度                                            |
| 16GB以上 VRAM              | Gemma 4（新）                 | `gemma4:12b` / `:26b` / `:31b`             | フロンティア級。26bはMoE、31bはDenseモデル。日本語精度は要検証                  |
| 日本語特化を試したい場合   | Llama-3.1-Swallow 8B instruct | `schroneko/llama-3.1-swallow-8b-instruct-v0.1` | 東工大/産総研、学術・法律文書に強い                                             |
| 同上（商用ライセンス重視） | ELYZA-japanese-Llama-3 8B     | `dsasai/llama3-elyza-jp-8b`                    | ELYZA社Llama-3ベース、商用利用条件が明確                                        |

```bash
docker compose exec ollama ollama pull qwen3:8b
```

⚠️ Gemma 4は2026年リリースの最新モデルで、公式には140以上の言語に対応するとされていますが、日本語での実運用ベンチマークはまだ乏しいため、本番導入前に自分のユースケースで**Qwen 3と比較検証**することを推奨します。

⚠️ Swallow・ELYZA系は**個人・コミュニティの名前空間**（`schroneko/...`、`dsasai/...`）で公開されているモデルで、Ollama公式が管理しているものではありません。導入する場合は、配布元のREADMEでライセンス条件（商用利用可否）とベースモデルの出所を確認してください。迷ったら公式ライブラリの`qwen3`系を主軸にするのが安全です。

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
