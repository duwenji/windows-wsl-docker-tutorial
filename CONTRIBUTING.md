# Contributing

このリポジトリへの改善提案や修正は歓迎します。

## 基本方針
- 初学者が段階的に学べる説明を優先してください。
- コマンド例はそのまま試せる形を意識してください。
- 大きな構成変更は `Issue` で背景共有してから進めてください。

## 推奨フロー
1. `Issue` を作成する
2. branch を切る
3. `docs/` 配下の教材を更新する
4. `./.github/skills-config/ebook-build/invoke-build.ps1` を実行する
5. `VALIDATION_CHECKLIST.md` を確認する
6. `Pull Request` を作成する

## レビュー観点
- コマンドやComposeファイルの例が再現可能か
- リンク切れや構成崩れがないか
- Windows・WSL2・Docker間のネットワーク層構造の説明に誤りがないか
- セキュリティや運用上の注意が十分か
- 学習順序が自然か
