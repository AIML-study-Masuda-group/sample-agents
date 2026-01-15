# AIML Research Template

LLM時代のAI/ML研究を加速するためのサンプルリポジトリです。

## コンセプト

新しい研究プロジェクトを始める際に、このリポジトリをcloneしてすぐに作業を開始できることを目指しています。

- **LLMとの協業を前提**: Claude Code、GitHub Copilotなどの生成AIツールと効率的に協業できる構成
- **研究の再現性**: mise によるツールバージョン管理で、環境差異による問題を排除
- **迅速な立ち上げ**: clone → mise install → 即座に研究開始

```bash
# 使い方
git clone https://github.com/ipnki/sample-research-repository.git my-research-project
cd my-research-project
mise install
```

---

## 概要（元sample-agentsの内容）

以下は元となったsample-agentsテンプレートの内容です。生成AIと人が効率的に協業できるようセットアップ方法をまとめています。

## 協業の基本方針

### 人の役割
- **方向性の提示**: 何を作りたいか、何を解決したいかを明確にする
- **計画の共有**: 目標とマイルストーンを生成AIと共有する
- **意思決定**: 仕様の判断、アーキテクチャの選択、優先順位の決定
- **レビュー**: 生成AIが作成したコードやドキュメントの品質確認

### 生成AIの役割
- **コーディング**: 実装、テストコード、設定ファイルの作成
- **レビュー**: コード品質チェック、潜在的な問題の指摘
- **ドキュメント更新**: コード変更に伴うドキュメントの同期
- **提案**: 複数の実装案の提示とトレードオフの説明

## 必要な環境

- [mise](https://mise.jdx.dev/) 2025.10.0 以上（ツールバージョン管理）
- Git
- プロジェクトに応じた追加ツール（例: gcloud, aws-cli, docker など）

## クイックスタート

### 1. このテンプレートをコピー

```bash
# 新しいプロジェクトディレクトリを作成
mkdir my-new-project
cd my-new-project

# sample-agents の内容をコピー
cp -r /path/to/sample-agents/* .

# Git リポジトリを初期化
git init
git add .
git commit -m "Initial commit from sample-agents template"
```

### 2. ツールのインストール

```bash
# mise で必要なツールを一括インストール
mise install
```

デフォルトで以下がインストールされます：
- Terraform 1.13.3
- GitHub CLI (gh) 2.81.0
- Python 3.11
- uv (latest)

**プロジェクトに応じてカスタマイズ**: [mise.toml](mise.toml) を編集して必要なツールを追加してください。

### 3. プロジェクト固有の設定

#### PLAN.md の作成

[PLAN.md](PLAN.md) を開き、あなたのプロジェクトの計画を記述してください：
- 目標・背景
- 実装する機能
- システム構成
- 実装フェーズ

#### RUNBOOK.md の更新

[RUNBOOK.md](RUNBOOK.md) にプロジェクト固有の運用ルールを追加してください：
- 環境準備手順
- 標準ワークフロー
- リリースチェックリスト

### 4. 生成AI との協業開始

生成AIに以下のように指示してください：

```
このプロジェクトは sample-agents テンプレートを使用しています。
PLAN.md に記載した目標を実現するため、一緒に実装を進めてください。

まずは PLAN.md を読んで、実装フェーズ1から始めましょう。
```

生成AIは自動的に以下のドキュメントを参照します：
- [AGENTS.md](AGENTS.md): 共通原則とワークフロー
- [RUNBOOK.md](RUNBOOK.md): 恒久ルールと運用手順
- [CLAUDE.md](CLAUDE.md): Claude Code 向け補足（他のAIも参考可）
- [CONTRIBUTING.md](CONTRIBUTING.md): 開発フローとレビュープロセス

## ドキュメント構成

このテンプレートには、生成AIと人が協業するための以下のドキュメントが含まれています：

### 人向けドキュメント
- **[README.md](README.md)**: プロジェクト概要とクイックスタート（このファイル）
- **[PLAN.md](PLAN.md)**: 実装計画書（目標、システム構成、実装フェーズ、費用見積もり）
- **[CONTRIBUTING.md](CONTRIBUTING.md)**: 開発フロー、ブランチ戦略、レビュープロセス

### 生成AI向けドキュメント
- **[AGENTS.md](AGENTS.md)**: 全生成AI共通の原則とワークフロー
- **[CLAUDE.md](CLAUDE.md)**: Claude Code 固有の補足ガイド
- **[.github/copilot-instructions.md](.github/copilot-instructions.md)**: GitHub Copilot 向けインストラクション

### 共通ドキュメント（人・AI両方が参照）
- **[RUNBOOK.md](RUNBOOK.md)**: 恒久ルールと運用手順（最優先で参照）

## 使い方のコツ

### 計画を先に立てる
PLANドキュメントに目標・機能・フェーズを明確に記述しておくと、生成AIが的確に実装をサポートしてくれます。

### 恒久ルールはRUNBOOKへ
「このルールは今後も守り続けたい」というものは、RUNBOOK.mdに追記してください。生成AIに「RUNBOOKに追記しますか？」と確認する習慣をつけましょう。

### Pull Request で履歴を残す
生成AIが作成したコードも必ずPR経由でレビューし、意図と背景をコメントに残しましょう。

### ドキュメントを更新し続ける
コード変更時は対応するドキュメントも同時に更新してください。生成AIがドキュメント更新をサポートしてくれます。

## カスタマイズ例

### プロジェクトタイプ別のツール構成

**Webアプリケーション（Next.js + TypeScript）の場合**:
```toml
[tools]
node = "20.x"
terraform = "1.13.3"
gh = "2.81.0"
```

**データパイプライン（Python + GCP）の場合**:
```toml
[tools]
python = "3.11"
uv = "latest"
terraform = "1.13.3"
gh = "2.81.0"
```

**インフラ管理（Terraform + AWS）の場合**:
```toml
[tools]
terraform = "1.13.3"
aws-cli = "2.x"
gh = "2.81.0"
```

## トラブルシューティング

### 生成AIが古い情報を参照している
→ RUNBOOK.md や PLAN.md が最新か確認してください。変更があれば生成AIに「RUNBOOKを読み直してください」と指示します。

### 生成AIの提案が期待と違う
→ PLAN.mdに具体的な要件を追記し、「PLANを確認して再提案してください」と依頼します。

### コマンドがうまく動かない
→ `mise doctor` でツールバージョンを確認し、RUNBOOK.mdの標準ワークフローに従っているか確認してください。

## ライセンス

MIT License（または必要に応じて変更してください）
