---
description: PR自動作成 - Unixパイプラインで効率化、Mermaid図自動生成
allowed-tools: ["Bash(gh:*)", "Bash(git:*)", "Bash(tee:*)", "Bash(xargs:*)", "Bash(pbcopy:*)", "TodoWrite"]
argument-hint: [-p | -u]（省略可）
---

# PR自動作成コマンド

## オプション判定
{{ option }} が指定されています。

## 初期情報収集とパイプライン処理

### ブランチとリポジトリ情報を効率的に取得
!`mkdir -p ~/.claude/logs && {
  echo "BRANCH=$(git branch --show-current)"
  echo "BASE=$(git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null | sed 's@^refs/remotes/origin/@@' || echo 'main')"
  echo "REPO=$(gh repo view --json nameWithOwner -q .nameWithOwner 2>/dev/null)"
  echo "STATUS=$(git status --porcelain | wc -l)"
} | tee ~/.claude/logs/pr-context.tmp`

## 動的コンテキスト取得

### 現在の状態
!`git status --short`

### 変更差分
!`BASE_BRANCH=$(git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null | sed 's@^refs/remotes/origin/@@' || echo 'main'); git diff $BASE_BRANCH...HEAD`

### コミット履歴
!`BASE_BRANCH=$(git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null | sed 's@^refs/remotes/origin/@@' || echo 'main'); git log --oneline $BASE_BRANCH..HEAD`

### PRテンプレート確認
@.github/pull_request_template.md または @~/.claude/.github/pull_request_template.md

## タスク実行

### 事前チェック
1. **Gitリポジトリとブランチ確認**
   - 現在のディレクトリがGitリポジトリか確認
   - 現在のブランチがmain/masterでないことを確認
   - 変更があることを確認

2. **GitHub CLI認証確認**
   - gh CLIがインストールされているか確認
   - 認証済みかどうか確認

### {{ option }}に基づく処理実行

#### オプションなし（デフォルト）

1. **事前確認**
```bash
git rev-parse --git-dir > /dev/null 2>&1 && \
CURRENT_BRANCH=$(git branch --show-current) && \
[ "$CURRENT_BRANCH" != "main" ] && [ "$CURRENT_BRANCH" != "master" ] && \
[ "$(git status --porcelain | wc -l)" -gt 0 ]
```

2. **PR説明文とMermaid図生成**
   以下の手順で包括的なPR説明文を作成：
   
   - **変更内容分析**: git diffの結果を詳細に分析
   - **ファイル変更パターンの判定**: 
     - 追加/削除/変更されたファイルの拡張子
     - 変更の規模と複雑さ
     - アーキテクチャへの影響

3. **Mermaid図の自動選択と生成**
   変更パターンに基づいて最適な図を選択：
   
   ```bash
   # ファイル変更パターンを分析
   BASE_BRANCH=$(git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null | sed 's@^refs/remotes/origin/@@' || echo 'main')
   git diff --name-status $BASE_BRANCH...HEAD | \
     awk '{print $2}' | \
     sed 's/.*\.//' | \
     sort | uniq -c
   ```
   
   - **フロントエンド変更** (js, ts, jsx, tsx, vue, react): flowchart LR
   - **API変更** (routes, controllers, api): sequenceDiagram
   - **データモデル変更** (models, schema, migration): classDiagram
   - **設定変更** (config, env, yaml): stateDiagram-v2
   - **複合的変更**: flowchart TD

4. **日本語PR説明文のフォーマット**
   ```markdown
   ## 概要
   [変更の背景と目的を簡潔に日本語で記述]

   ## 変更内容
   [具体的な変更点を箇条書きで]
   - 追加: [新機能や新ファイル]
   - 修正: [バグ修正や改善]
   - 削除: [不要なコードや機能の削除]

   ## 技術的詳細
   
   ### 変更のアーキテクチャ図
   ```mermaid
   [自動生成されたMermaid図]
   ```

   ## テスト項目
   - [ ] 単体テスト実行
   - [ ] 統合テスト実行
   - [ ] 動作確認完了
   - [ ] 既存機能への影響確認

   ## 関連情報
   - Base Branch: [ベースブランチ名]
   - Feature Branch: [現在のブランチ名]
   - Files Changed: [変更ファイル数]
   ```

5. **PR作成**
```bash
gh pr create --draft \
  --title "$(git log --oneline -1 | sed 's/^[a-f0-9]* //')" \
  --body "$(cat ~/.claude/tmp/pr-body.md)"
```

#### -p オプション（Push付き）

1. **効率的なpushとブランチ名の保持**
```bash
echo "🚀 ブランチをpushします..."
git branch --show-current | \
  tee >(pbcopy) | \
  tee >(echo "[$(date +%Y-%m-%d\ %H:%M:%S)] Pushing branch: $(cat)" >> ~/.claude/logs/pr-history.log) | \
  xargs -I{} sh -c 'echo "Pushing: {}" && git push -u origin {}'
```

2. **push結果の確認**
```bash
if [ $? -eq 0 ]; then
  echo "✅ Push successful - Branch: $(pbpaste)"
  echo "📝 PR作成を開始します..."
else
  echo "❌ Push failed"
  exit 1
fi
```

3. **PR作成**（デフォルトと同じ処理）

#### -u オプション（既存PR更新）

1. **既存PR番号の自動取得**
```bash
CURRENT_BRANCH=$(git branch --show-current)
PR_NUMBER=$(gh pr list --head $CURRENT_BRANCH --json number -q '.[0].number')

if [ -z "$PR_NUMBER" ]; then
  echo "❌ 現在のブランチ($CURRENT_BRANCH)に関連するPRが見つかりません"
  exit 1
else
  echo "📝 PR #$PR_NUMBER を更新します..."
fi
```

2. **更新処理**
```bash
gh pr edit $PR_NUMBER --body "$(cat ~/.claude/tmp/pr-body.md)"
echo "✅ PR #$PR_NUMBER の説明を更新しました"
```

## 処理完了後のログ記録

```bash
# PR作成成功時の記録
echo "$(date +%Y-%m-%d\ %H:%M:%S),$(git branch --show-current),$(gh pr view --json number -q .number 2>/dev/null),$(git log --oneline -1)" >> ~/.claude/logs/pr-created.csv

# 作業完了メッセージ
echo ""
echo "🎉 PR処理が完了しました!"
echo "📊 ブランチ: $(git branch --show-current)"
echo "📝 PR URL: $(gh pr view --json url -q .url 2>/dev/null)"
echo "🔗 クリップボード: $(pbpaste 2>/dev/null || echo 'N/A')"
```

---

**このコマンドは以下の機能を提供します:**

✅ **自動情報収集**: Git履歴とステータスを自動取得  
✅ **効率的なpush**: Unixパイプラインでブランチ名を再利用  
✅ **Mermaid図生成**: 変更内容に応じた最適な図を自動選択  
✅ **ログ管理**: 全操作履歴を記録・追跡可能  
✅ **エラーハンドリング**: 各ステップで失敗を検知・報告  
✅ **クリップボード連携**: ブランチ名を即座に再利用可能

## TodoWrite使用義務

複雑なタスクの場合、必ずTodoWriteツールでphase管理を行ってください：

1. **タスク開始時**: 全phaseをTodoWriteに登録
2. **Phase実行中**: 該当phaseをin_progressに変更
3. **Phase完了時**: 即座にcompletedに変更
4. **新Phase追加時**: 必要に応じてTodoWriteを更新

詳細は`~/.claude/docs/todowrite-specification.md`を参照。

**使用例:**
- `/pr` - PR作成のみ
- `/pr -p` - push後にPR作成（ブランチ名をクリップボードに保存）
- `/pr -u` - 既存PRの説明文を更新