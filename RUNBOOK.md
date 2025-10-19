# RUNBOOK

## 1. 位置づけ
この Runbook はプロジェクトの運用と恒久ルールを定義する一次情報です。人間のコントリビュータと AI エージェントの双方が参照し、疑問があれば本書を基準に判断します。

## 2. ドキュメント体系
- `README.md`: プロジェクト概要とクイックスタート
- `PLAN.md`: 実装計画書（アーキテクチャ、費用見積もり、実装手順の詳細）
- `CONTRIBUTING.md`: 人の開発フローとレビュー手順
- `AGENTS.md` / `CLAUDE.md`: エージェント向け実務ガイド
- 本 Runbook: 恒久ルール・標準手順・チェックリスト（**本書が最優先**）

## 3. 変更管理と恒久ルール

### 恒久ルール追加のフロー
1. 新しい恒久ルールが必要か判断したら、まず関係者と合意形成を行う
2. AI も人も、恒久化前に「RUNBOOK.md に追記しますか？」と必ず確認する
3. YES の場合は Runbook を更新し、関連ドキュメントがあれば同期する
4. 変更内容は Pull Request で共有し、レビュアーが適用手順と影響範囲を確認する

### ドキュメント更新ルール
コード変更時は対応するドキュメントも同時に更新する（詳細は `AGENTS.md` の「ドキュメント更新の原則」を参照）

## 4. 環境準備

> **注意**: 以下はサンプルです。実際のプロジェクトに合わせて書き換えてください。

### ツールのインストール
```bash
# mise で必要なツールを一括インストール
mise install

# これにより以下がインストールされます（mise.toml を参照）:
# - プロジェクトに必要なツール（例: Python, Node.js, Terraform, gh など）
```

### プロジェクト固有のセットアップ

**Python プロジェクトの場合**:
```bash
# 依存関係のインストール
mise run py:sync

# テスト実行
mise run py:test
```

**Node.js / TypeScript プロジェクトの場合**:
```bash
# 依存関係のインストール
npm install

# ビルド
npm run build

# テスト実行
npm test
```

**Terraform プロジェクトの場合**:
```bash
# 初期化
mise run tf:init

# フォーマット確認
mise run tf:fmt

# 構文検証
mise run tf:validate
```

### 認証設定

**GCP を使う場合**:
```bash
# プロジェクトを設定
gcloud config set project YOUR_PROJECT_ID

# Application Default Credentials を取得
gcloud auth application-default login --project=YOUR_PROJECT_ID
```

**AWS を使う場合**:
```bash
# プロファイルを設定
export AWS_PROFILE=your-profile-name

# 認証確認
aws sts get-caller-identity
```

**GitHub CLI を使う場合**:
```bash
# GitHub 認証
mise run gh:auth
```

### API の有効化（該当する場合）

**GCP の場合**:
```bash
# 必要な API を有効化
gcloud services enable \
  cloudresourcemanager.googleapis.com \
  --project=YOUR_PROJECT_ID

# 他の必要な API を追記してください
```

## 5. Git・ブランチ戦略

### 基本方針
ブランチ戦略の詳細は [`CONTRIBUTING.md`](CONTRIBUTING.md#ブランチ戦略) を参照してください。ここでは運用上の恒久ルールと手順を定義します。

### ブランチ運用ルール
1. **`main` ブランチ**: 本番環境にデプロイ済みのコード。直接コミット禁止
2. **`feature/*` ブランチ**: 機能追加や修正作業用。`main` から派生し、完了後 `main` へマージ
3. **`<agent-name>/*` ブランチ**: AI エージェントが作業する場合の作業ブランチ
   - 例: `claude/add-user-auth`, `copilot/refactor-api`

### ブランチ作成手順
```bash
# 機能開発ブランチ（人が作業）
git checkout main
git pull origin main
git checkout -b feature/add-user-auth

# AI 作業ブランチ（Claude が作業）
git checkout main
git pull origin main
git checkout -b claude/add-user-auth
```

### Pull Request の作成

#### GitHub CLI (gh) を使う方法（推奨）

```bash
# 初回のみ: GitHub CLI の認証
mise run gh:auth

# PR 作成（Web ブラウザで開く）
gh pr create --base main --title "PR タイトル" --body "PR の説明"
```

#### Web UI で作成する方法

1. GitHub リポジトリページにアクセス
2. **base** を `main` に設定
3. **compare** を現在のブランチに設定
4. "Create pull request" をクリック

### マージとレビュー
- すべてのマージは Pull Request 経由で実施
- AI が作成したブランチは、人が必ず内容を確認してから `main` へマージ
- `main` へのマージは、リリースチェックリスト（後述）を満たすこと

## 6. 標準ワークフロー

> **注意**: 以下はサンプルです。実際のプロジェクトに合わせて書き換えてください。

### Python プロジェクトの開発フロー

```bash
# コード整形
mise run py:lint

# テスト実行
mise run py:test

# カバレッジ確認
mise run py:coverage
```

### Node.js / TypeScript プロジェクトの開発フロー

```bash
# リント
npm run lint

# テスト
npm test

# ビルド
npm run build
```

### Terraform プロジェクトの開発フロー

```bash
# コード整形
mise run tf:fmt

# 構文検証
mise run tf:validate

# 差分確認
mise run tf:plan

# 適用（承認者の指示後のみ）
mise run tf:apply
```

**注意事項**:
- `mise run tf:apply` は承認者の明示的な指示がある場合のみ実施し、結果を記録
- 破壊的な変更（リソース削除など）は事前に影響範囲を確認し、承認を得る

### mise によるバージョン統一（重要）
本プロジェクトでは、人間と AI エージェントが同一バージョンのツールを使用するため、**すべてのコマンド実行は mise 経由で行う**ことを原則とします。

#### mise タスクの使用（推奨）
`mise.toml` で定義されたタスクを使用することで、自動的に正しいバージョンのツールが使用されます。

#### カスタムコマンドの実行
mise.toml に定義されていないコマンドを実行する場合は、`eval "$(mise activate bash)"` で mise 環境を有効化してから実行します：

```bash
# 例: カスタムコマンドを実行
eval "$(mise activate bash)" && <your-command>
```

#### 注意事項
- 直接コマンドを実行すると、システムにインストールされた古いバージョンが使われる可能性があります
- 新しいコマンドが頻繁に必要になる場合は、`mise.toml` の `[tasks]` にタスクとして追加することを検討してください

## 7. リリースチェックリスト

> **注意**: 以下はサンプルです。実際のプロジェクトに合わせて書き換えてください。

### Python プロジェクトの場合
- [ ] `mise run py:lint` が成功（コードが整形されている）
- [ ] `mise run py:test` が成功（すべてのテストがパスしている）
- [ ] 影響範囲とロールバック手順を PR に記載し、レビュー済み

### Node.js / TypeScript プロジェクトの場合
- [ ] `npm run lint` が成功（リントエラーがない）
- [ ] `npm test` が成功（すべてのテストがパスしている）
- [ ] `npm run build` が成功（ビルドエラーがない）
- [ ] 影響範囲とロールバック手順を PR に記載し、レビュー済み

### Terraform プロジェクトの場合
- [ ] `mise run tf:fmt` が成功（コードが整形されている）
- [ ] `mise run tf:validate` が成功（構文エラーがない）
- [ ] `mise run tf:plan` に想定外の差分がない
- [ ] 影響範囲とロールバック手順を PR に記載し、レビュー済み

## 8. セキュリティ・データ保護
- 秘密情報（鍵・トークン・個人情報）を Git に保存しない
- 認証情報は Secret Manager や環境変数で管理する
- IAM 権限の変更は事前に影響範囲を確認し、承認を得る
- 破壊的操作（リソース削除、データベース削除等）は事前に承認を得る

## 9. エスカレーション

> **注意**: 以下はサンプルです。実際のプロジェクトに合わせて書き換えてください。

問題が発生した場合のエスカレーション先を定義します：

- **インフラ関連**: インフラ担当者 (@example-user)
- **セキュリティ関連**: セキュリティ担当者 (@security-team)
- **本番障害**: オンコール担当者 (Slack: #oncall)
- **重大インシデント**: プロジェクトオーナー (@owner)

## 10. 更新履歴

> **注意**: プロジェクト固有の更新履歴を記録してください。

- YYYY-MM-DD: 初版作成（sample-agents テンプレートから生成）
- YYYY-MM-DD: セキュリティポリシーを追加
- YYYY-MM-DD: リリースチェックリストを更新
