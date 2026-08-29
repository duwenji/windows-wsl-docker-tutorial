# 10 Local Rag Anythingllm Ollama

## この章で学ぶこと

- AnythingLLM + OllamaによるローカルRAG環境の全体構成とGPU passthrough
- RAG精度を左右する日本語対応Embeddingモデルの選び方
- 日本語チャット用LLMモデルの選び方とハードウェア要件
- ワークスペース作成からドキュメント取り込み・RAG動作確認までの一連の流れ

## 学習目標

- WSL2 + Docker EngineでAnythingLLMとOllamaを連携させて起動できる
- 用途とハードウェアに応じてEmbedding/LLMモデルを選定できる
- AnythingLLMでRAGワークスペースを作成し、日本語ドキュメントに対する質問応答を検証できる

## 演習案

1. docker-composeでOllama + AnythingLLMを起動し、Windows側ブラウザから接続する
2. `qwen3-embedding`と`qwen3`（または自分のハードウェアに合ったモデル）をpullし、AnythingLLMに設定する
3. 日本語ドキュメントをワークスペースに取り込み、内容に基づいた質問に正しく回答できることを確認する

## 成功判定

- [03-rag-workspace-and-verification.md](./03-rag-workspace-and-verification.md) の章末チェックリストの全項目にチェックが付けられる

## 次へ

- [../../ROADMAP.md](../../ROADMAP.md)
