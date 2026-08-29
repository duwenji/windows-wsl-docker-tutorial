# 04 改行コードとGitの注意点

## 所要時間

約15分

## 学習目標

- CRLF混入によるトラブルの原因を説明できる
- `.gitattributes`での対処法を実践できる

---

## 改行コード問題が起きる理由

Windows上のGit設定（`core.autocrlf=true`など）でリポジトリをcloneし、そのフォルダをWSL/コンテナ内で使うと、テキストファイルの改行コードがCRLF（`\r\n`）のまま混入することがあります。

これが引き起こす典型的な問題:

- シェルスクリプト（`.sh`）の1行目に`\r`が混入し、`bad interpreter: /bin/bash^M: no such file or directory`のような実行エラーになる
- 意図しない改行コード変更が原因で、実際には変更していない行までdiffに表示される（コードレビューのノイズになる）

## 対処: WSL側で直接cloneする

最も確実な対処は、Windows側でcloneしたリポジトリ（`/mnt/c/...`）を使うのではなく、**WSL側で直接`git clone`し直す**ことです。

```bash
cd ~/projects
git clone <repository-url>
```

これにより、Gitのcore.autocrlf設定に関わらず、Linux向けの標準的な改行コード（LF）でファイルが展開されます。

## .gitattributesでの明示

プロジェクト側でも改行コードを明示しておくと、開発者の環境に依存せず一貫した挙動になります。

```
* text=auto eol=lf
*.sh text eol=lf
```

これにより、テキストファイルは常にLFで管理され、シェルスクリプトも確実にLFになります（このリポジトリ自体の`.gitattributes`も同じ方針で設定されています）。

## 演習

1. 意図的にCRLF付きのシェルスクリプトを用意する

```bash
printf '#!/bin/bash\r\necho hello\r\n' > broken.sh
chmod +x broken.sh
./broken.sh
```

`bad interpreter`のようなエラーが出ることを確認します。

2. `dos2unix broken.sh`（または`sed -i 's/\r$//' broken.sh`）で改行コードを修正し、再実行してエラーが解消することを確認する
3. プロジェクトに`.gitattributes`を追加し、以後同じ問題が起きないことを確認する

## 章末チェックリスト

- [ ] CRLF混入が引き起こす具体的なエラーを再現できた
- [ ] `.gitattributes`での対処法を実践できた
- [ ] WSL側で直接cloneすることの重要性を説明できる

## 次へ

- [06-compose-multi-service/README.md](../06-compose-multi-service/README.md)
