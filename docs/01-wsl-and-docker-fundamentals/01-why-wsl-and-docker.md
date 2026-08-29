# 01 なぜWSLとDockerを組み合わせるのか

## この章で学ぶこと

- Dockerとは何か（コンテナはプロセス隔離であり仮想マシンではないこと）
- DockerがLinuxカーネルの機能に依存していること
- なぜWindows上でDockerを使うのにWSLが必要なのか
- Docker Desktop / WSL+Docker Engine / Hyper-V VM という3つの選択肢の違い

## Dockerとは何か

Dockerは「コンテナ」という単位でアプリケーションを実行する技術です。よく仮想マシンと混同されますが、仕組みはまったく異なります。

- **仮想マシン**: ハードウェアをエミュレートし、その上でOSカーネルごと丸ごと起動する
- **コンテナ**: ホストと同じLinuxカーネルを共有しながら、プロセスの見える範囲（ファイルシステム、ネットワーク、プロセスID空間など）だけを隔離する

コンテナは「軽量な隔離されたプロセス」であり、仮想マシンのようにOS全体を起動するわけではありません。この違いが、コンテナの起動が数秒で終わる理由です。

`image`（イメージ）はコンテナを起動するための読み取り専用のテンプレートで、`container`（コンテナ）はそのイメージから実際に起動された実行中のインスタンスです。

## なぜDockerにLinuxカーネルが必要か

Dockerのコンテナ隔離は、Linuxカーネルが持つ以下の機能の組み合わせで実現されています。

- **namespaces**: プロセスから見えるファイルシステム、ネットワーク、プロセスID、ホスト名などを個別に分離する機能
- **cgroups**: CPU・メモリなどのリソース使用量を制限・計測する機能

⚠️ これらはLinuxカーネル固有の機能です。Windowsカーネルには同等の機能がありません。つまり、**WindowsネイティブではLinux向けのDockerコンテナを直接動かせない**という制約があります。

## なぜWSLが必要か

上記の制約を解決するために、WindowsでLinuxコンテナを動かすには「本物のLinuxカーネル」をどこかで用意する必要があります。

WSL（Windows Subsystem for Linux）、特にWSL2は、Windows上で実際のLinuxカーネルを動かす仕組みを提供します。これにより、WSL2上でDocker Engineを動かせば、Linuxカーネルのnamespaces/cgroupsをそのまま利用してコンテナを起動できるようになります。

つまり、**Docker自体はLinuxの技術であり、WSL2はWindows上にそのLinux環境を持ち込む役割を担っている**、という位置づけです。この関係は次のレッスンでさらに詳しく見ていきます。

## Docker Desktop / WSL+Docker Engine / Hyper-V VM の3つの選択肢比較

Windows上でDockerを使う方法は、大きく3つあります。

| 方式 | Linuxカーネルの実体 | GUI | ライセンス | 本教材での採用 |
|---|---|---|---|---|
| Docker Desktop（WSL2 backend） | WSL2 VM内で自動管理 | あり | 大企業では商用ライセンスが必要な場合がある | 比較コラムのみ |
| **WSL + Docker Engine直接インストール** | WSL2 VM内に自分でインストール | なし（CLI） | 無料（Docker Engine自体はOSS） | ✅ 本教材の主軸 |
| Hyper-V VM（フルVM） + Docker | 独立したHyper-V仮想マシン | 環境次第 | 無料 | 扱わない（WSL2よりリソース消費が大きい） |

✅ 本教材では、内部の仕組みが見える学習効果と、企業ライセンス費用の回避という2つの理由から、**WSL内へのDocker Engine直接インストール**を主軸に進めます。Docker Desktopとの違いは02章で改めて比較コラムとして扱います。

## 次へ

- [02-wsl-architecture.md](./02-wsl-architecture.md)
