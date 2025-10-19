# CLAUDE.md

このファイルは Claude Code (claude.ai/code) がこのリポジトリで作業する際の補足ガイドです。基本方針は `AGENTS.md` と `RUNBOOK.md` を最優先で参照してください。

## 基本方針

Claude Code はこのプロジェクトで以下の役割を担います：
- コードの実装とテスト作成
- Pull Request のレビューと改善提案
- ドキュメントの更新と同期
- トラブルシューティングのサポート

**重要**: 作業を開始する前に、必ず以下を読んでください：
1. `PLAN.md` - プロジェクトの目標と実装フェーズ
2. `RUNBOOK.md` - 恒久ルールと標準ワークフロー
3. `AGENTS.md` - 共通原則とコミュニケーション方法

## セットアップ

### 初回セットアップ
1. `mise install` でプロジェクトに必要なツールをインストール
2. プロジェクト固有の認証設定（RUNBOOK.md を参照）
3. `PLAN.md` を読み、現在の実装フェーズを確認

### プロジェクト例：Python + GCP の場合
1. `mise install` で Python / uv / gh をインストール
2. `mise run py:freeze` で pyproject.toml から requirements.txt を生成
3. `gcloud auth application-default login` で ADC を取得
4. `gcloud config set project YOUR_PROJECT_ID` でプロジェクトを設定

### プロジェクト例：Node.js + TypeScript の場合
1. `mise install` で Node.js / npm をインストール
2. `npm install` で依存関係をインストール
3. `npm run build` でビルドが通ることを確認

## 作業時の注意

### ⚠️ mise によるバージョン統一（重要）
- **すべてのコマンドは mise 経由で実行すること**
  - これにより、人間と AI エージェントが同一バージョンのツールを使用できます
  - 直接コマンドを実行すると、システムにインストールされた古いバージョンが使われる可能性があります
- **推奨**: mise.toml で定義されたタスクを使用
  - 例: `mise run tf:plan`, `mise run py:test`, `mise run gh:pr`
- **カスタムコマンドが必要な場合**:
  - `eval "$(mise activate bash)" && <command>` で mise 環境を有効化してから実行
  - 理由を説明し、mise タスクへの追加を検討する

### コマンド実行の原則

#### プロジェクトタイプ別の標準コマンド

**Terraform プロジェクトの場合**:
```bash
mise run tf:fmt       # コード整形
mise run tf:validate  # 構文検証
mise run tf:plan      # 差分確認
mise run tf:apply     # 適用（ユーザーの明示指示がある場合のみ）
```

**Python プロジェクトの場合**:
```bash
mise run py:sync      # 依存関係インストール
mise run py:freeze    # requirements.txt 生成（デプロイ用）
mise run py:test      # テスト実行
mise run py:lint      # リント実行
```

**Node.js / TypeScript プロジェクトの場合**:
```bash
mise run node:install # 依存関係インストール（npm install相当）
mise run node:build   # ビルド
mise run node:test    # テスト実行
mise run node:lint    # リント実行
```

**共通コマンド**:
```bash
mise run gh:pr        # Pull Request 作成
mise run gh:auth      # GitHub 認証
```

### 生成物の取り扱い
- 秘密情報を含めない
- ログや一時ファイルは `.gitignore` を確認して扱う
- デプロイ用のビルド成果物は適切なディレクトリに配置する

## 変更提案・レビュー

### 提案の仕方
- 恒久ルールの変更提案時は「RUNBOOK に追記しますか？」と確認してから実装
- 複数案が考えられる場合は以下の形式でトレードオフを提示する：
  ```
  【案1】〇〇の方法
  メリット: ...
  デメリット: ...

  【案2】△△の方法
  メリット: ...
  デメリット: ...

  推奨: 案1（理由：...）
  ```

### Pull Request の作成
PR には以下を必ず含める：
- **概要**: 何を変更したか、なぜ変更したか
- **変更内容**: どのファイルをどう変更したか
- **テスト結果**: `mise run py:test` や `mise run tf:plan` の実行結果
- **影響範囲**: この変更が他のシステムに与える影響
- **ロールバック方法**: 問題が発生した場合の戻し方
- **更新したドキュメント**: README.md、RUNBOOK.md、PLAN.md など

### レビュー対応
- レビューコメントには丁寧に回答する
- 修正が必要な場合は速やかに対応する
- 合意が得られない場合は、追加の説明や代替案を提示する

## トラブルシューティング

### プロジェクトタイプ別のトラブルシューティング

**Terraform の場合**:
- `TF_LOG=TRACE` や `terraform state pull` は読み取り目的でのみ使用
- 状態変更が必要な場合は人に相談
- API エラーが発生した場合は、必要な API が有効化されているか確認（RUNBOOK 参照）

**Python の場合**:
- 依存関係エラー: `mise run py:sync` を再実行
- テスト失敗: `mise run py:test` でローカルで再現し、原因を特定
- パッケージ追加後は必ず `mise run py:freeze` を実行

**Node.js の場合**:
- ビルドエラー: `mise run node:build` を再実行
- 型エラー: `tsc --noEmit` で型チェック
- テスト失敗: `mise run node:test` でローカルで再現

### 共通のトラブルシューティング
- **ツールバージョンが合わない**: `mise doctor` でバージョンを確認
- **権限エラー**: RUNBOOK.md の認証手順を再確認
- **想定外のエラー**: エラーメッセージ全文と再現手順を人に報告

## まとめ

Claude Code からの作業は常に以下を心がけてください：
1. **RUNBOOK を基準**にして安全に進める
2. **人間のオーナーシップを尊重**し、判断が必要な場合は必ず確認する
3. **ドキュメントを常に最新**に保ち、変更と同期する
4. **PR で履歴を残し**、背景と意図を明確にする

疑問があれば遠慮なく質問し、安全第一で作業を進めてください。
