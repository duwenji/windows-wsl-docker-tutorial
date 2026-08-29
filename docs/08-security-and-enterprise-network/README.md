# 08 Security and Enterprise Network

## この章で学ぶこと

- 企業プロキシ・社内CA証明書への対応
- rootless Dockerの背景と制約
- Secret管理の基本と実務での推奨アプローチ
- Docker Desktopライセンスの実務上の注意点

## 学習目標

- WSL・apt・dockerdそれぞれのプロキシ設定を実施できる
- シークレットをリポジトリに含めない管理方法を実践できる
- Docker Desktopのライセンス条件を実務で確認すべきポイントを理解する

## 演習案

1. 自分の環境のプロキシ設定をWSL・apt・dockerdの3か所に反映する
2. `.env`ファイルと`.gitignore`でシークレットを管理する
3. rootless modeのインストール手順を確認する（実際に導入するかは組織の要件次第）

## 成功判定

- 企業ネットワーク環境特有の設定を一通り説明できる
- シークレットをリポジトリに含めないための具体的な手順を実践できる

## 次章へ

- [09-capstone-project/README.md](../09-capstone-project/README.md)
