---
description: 自動コミット - 変更内容をAIが要約して日本語でコミット
allowed-tools: ["Bash", "Read", "TodoWrite"]
---

# 自動コミット実行

## 📋 Prerequisites（前提条件）
- [ ] [PRE-001] Gitリポジトリ確認
  - コマンド：`git rev-parse --git-dir`
  - 期待：エラーなく実行される（Gitリポジトリ内で実行）
- [ ] [PRE-002] 未コミット変更の存在確認
  - コマンド：`git status --porcelain | wc -l`
  - 期待：0より大きい値（変更があることを確認）
- [ ] [PRE-003] git config設定確認
  - コマンド：`git config user.name && git config user.email`
  - 期待：両方が設定されている

## 1. 現在の変更状況を確認
!git status --porcelain

## 2. すべての変更をステージング
!git add -A

## 3. ステージングされた変更の差分を取得
!git diff --cached

## 4. コミットメッセージ生成とコミット実行
上記の差分内容を分析し、以下を実行：
1. 変更内容を日本語で要約（25字以内）
2. その要約をコミットメッセージとして使用
3. `git commit -m "生成したメッセージ"` を実行

## 5. コミット後の状態確認
!git status --porcelain

## TodoWrite使用義務

複雑なタスクの場合、必ずTodoWriteツールでphase管理を行ってください：

1. **タスク開始時**: 全phaseをTodoWriteに登録
2. **Phase実行中**: 該当phaseをin_progressに変更
3. **Phase完了時**: 即座にcompletedに変更
4. **新Phase追加時**: 必要に応じてTodoWriteを更新

詳細は`~/.claude/docs/todowrite-specification.md`を参照。

## ✅ Validation（検証項目）
- [ ] [VAL-001] コミット作成確認
  - コマンド：`git log -1 --oneline`
  - 確認内容：最新コミットが今回作成したもの
  - 成功条件：生成したメッセージでコミットが作成されている
- [ ] [VAL-002] 作業ディレクトリクリーン確認
  - コマンド：`git status --porcelain`
  - 確認内容：未コミットの変更が残っていない
  - 成功条件：出力が空（クリーンな状態）