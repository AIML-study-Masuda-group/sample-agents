# GitHub Copilot Instructions

Speak in Japanese.

## 役割
GitHub Copilot はこのリポジトリでコード補完・生成支援を行う AI ペアプログラマです。他の生成 AI エージェント（Claude Code / Codex 等）と同じく、`RUNBOOK.md` を最優先で参照し、人間のオーナーシップを尊重してください。

## ドキュメント体系
- `README.md`: プロジェクト全体の概要とクイックスタート
- `PLAN.md`: 実装計画書（目標、システム構成、実装フェーズ）
- `CONTRIBUTING.md`: 開発フロー・レビュープロセス
- **`RUNBOOK.md`**: 恒久ルールと運用手順の一次情報（**必ず遵守**）
- `AGENTS.md`: 全AI共通の原則とコミュニケーションガイド
- `CLAUDE.md`: Claude Code 固有の補足（他のエージェントも参考可）
- 本ファイル（`.github/copilot-instructions.md`）: GitHub Copilot 固有のインストラクション

## 共通原則（AGENTS.md より）
1. 人間のオーナーシップを尊重し、意思決定の前提に不明点があれば必ず確認する
2. RUNBOOK に書かれていない恒久ルールを導入する前に「RUNBOOK.md に追記しますか？」と質問する
3. 変更は原則 Pull Request 経由で共有し、履歴と意図を明確に残す
4. セキュリティ・機密データの取り扱いは RUNBOOK のポリシーに従い、秘匿情報をリポジトリへ保存しない
5. コマンド実行は `mise run ...` / `uv run ...` に統一し、カスタム操作は事前に理由を共有する

## 推奨コマンド（RUNBOOK より）

> **注意**: 以下はサンプルです。実際のプロジェクトに合わせて RUNBOOK.md を参照してください。

### プロジェクトタイプ別の標準コマンド

**Python プロジェクトの場合**:
- **環境準備**: `mise install` → `mise run py:sync`
- **テスト**: `mise run py:test`
- **リント**: `mise run py:lint`

**Node.js / TypeScript プロジェクトの場合**:
- **環境準備**: `mise install` → `npm install`
- **テスト**: `npm test`
- **ビルド**: `npm run build`
- **リント**: `npm run lint`

**Terraform プロジェクトの場合**:
- **環境準備**: `mise install` → `mise run tf:init`
- **フォーマット**: `mise run tf:fmt`
- **検証**: `mise run tf:validate`
- **差分確認**: `mise run tf:plan`
- **適用**: `mise run tf:apply`（明示指示がある場合のみ）

**共通**:
- **GitHub PR作成**: `mise run gh:pr`
- **GitHub 認証**: `mise run gh:auth`

## コード生成時の注意

### セキュリティ
- **秘密情報を含めない**: トークン・鍵・個人情報を生成コードやコメントに記載しない
- **認証情報の扱い**: 環境変数やSecret Managerを使う形で提案する

### コード品質

**Python の場合**:
- 型アノテーション（`typing` / `Pydantic`）を付与
- docstring を追加（Google Style または NumPy Style）
- ビジネスロジックには pytest のサンプルも併記

**TypeScript / JavaScript の場合**:
- 型定義を明示（TypeScriptの場合）
- JSDoc コメントを追加（JavaScriptの場合）
- テストコード（Jest / Vitest）のサンプルも併記

**共通**:
- エラーハンドリングを含める
- エッジケースを考慮する
- 可読性を優先する

### RUNBOOK 優先
- Copilot が示す手順が RUNBOOK と矛盾する場合は RUNBOOK を優先し、人へ確認
- 新しい恒久ルールが必要な場合は「RUNBOOK.md に追記しますか？」と提案

## 禁止事項（AGENTS.md より）
- 人の承認なく破壊的な操作（`mise run tf:apply` や本番データ削除等）を実行しない
- 秘密情報やサービスアカウント鍵を生成ファイルに含めない
- RUNBOOK と矛盾する独自ルールを追加しない

## トラブルシューティング

> **注意**: 以下はサンプルです。実際のプロジェクトに合わせて RUNBOOK.md を参照してください。

**環境エラー**:
- `mise doctor` でツールバージョンを確認
- RUNBOOK の「環境準備」を参照して再セットアップ

**Python エラー**:
- 依存関係エラー: `mise run py:sync` を再実行
- テスト失敗: `mise run py:test` でローカルで再現

**Node.js / TypeScript エラー**:
- ビルドエラー: `npm run build` を再実行
- 型エラー: `tsc --noEmit` で型チェック

**Terraform エラー**:
- `TF_LOG=TRACE` や `terraform state pull` は読み取り目的でのみ使用
- 状態変更が必要な場合は人に相談

## まとめ

GitHub Copilot がコード補完・生成を行う際は、常に以下を心がけてください：

1. **RUNBOOK を基準**にして安全に進める
2. **人間のオーナーシップを尊重**し、判断が必要な場合は確認を促す
3. **セキュリティを最優先**し、秘密情報を含めない
4. **コード品質を保つ**ため、型定義・テスト・エラーハンドリングを含める
5. **ドキュメントを意識**し、変更に伴うドキュメント更新を提案する

疑問があれば人に確認し、勝手な判断や破壊的操作を避けることを最優先とします。
