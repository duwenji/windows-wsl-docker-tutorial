# QUICK REFERENCE

## 学習順序

1. 01 WSL and Docker Fundamentals
2. 02 Environment Setup
3. 03 Docker Core Operations
4. 04 Networking Deep Dive
5. 05 Dev Workflow and File Sharing
6. 06 Compose Multi Service
7. 07 Operations and Troubleshooting
8. 08 Security and Enterprise Network
9. 09 Capstone Project

## 章末チェック共通項目

- 学習目標を自分の言葉で説明できる
- 演習が再現できる
- 確認コマンドで結果を確認できる
- 失敗シナリオの原因を説明できる

## 主要確認コマンド

- `docker ps -a`
- `docker logs -f <container>`
- `docker network inspect <network>`
- `docker compose logs -f`
- `wsl -l -v` / `wsl --shutdown`

## トラブルシュート優先順

1. コンテナ状態確認（`docker ps -a`）
2. ログ確認（`docker logs`）
3. ネットワーク層の切り分け（コンテナ→bridge→WSL2 VM→Windows）
4. リソース確認（`docker stats`, `.wslconfig`）
5. WSL自体の再起動（`wsl --shutdown`）
