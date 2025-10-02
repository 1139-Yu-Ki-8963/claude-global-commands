---
description: 専門思考エージェントによる高度な分析と計画
allowed-tools: ["Task", "Read", "Grep", "Glob", "WebSearch", "TodoWrite"]
argument-hint: [分析・計画対象の説明]
---

# 高度思考プロセス実行

問題の性質に応じて適切な思考エージェントで分析・計画・解決を実行します。

$ARGUMENTS

## 分析フェーズ
@think-analyzer 問題の根本原因分析、多角的視点での評価、パターンと傾向の発見、制約と前提条件の明確化

## 計画フェーズ  
@think-planner 実装計画の詳細設計、タスクの優先順位付け、リソース配分の最適化、実行可能なステップへの分解

## 解決フェーズ
@think-solver 実践的な解決策の生成、代替案の比較検討、実装可能性の評価、成功指標の定義

複雑な問題の場合は、上記3つのエージェントを並列実行し、統合的な解決策を導きます。

## TodoWrite使用義務

複雑なタスクの場合、必ずTodoWriteツールでphase管理を行ってください：

1. **タスク開始時**: 全phaseをTodoWriteに登録
2. **Phase実行中**: 該当phaseをin_progressに変更
3. **Phase完了時**: 即座にcompletedに変更
4. **新Phase追加時**: 必要に応じてTodoWriteを更新

詳細は`~/.claude/docs/todowrite-specification.md`を参照。