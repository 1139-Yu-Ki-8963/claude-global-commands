---
description: 包括的な調査を実行し、適切な専門エージェントに振り分け
allowed-tools: ["Task"]
argument-hint: [調査内容]
---

# 包括的調査実行

## 調査内容
$ARGUMENTS

## 実行方法

問題を分析して、最適な調査パターンを選択します。

### キーワード判定と専門エージェント選択

#### Pattern 1: Library/Framework Research
キーワード: ライブラリ、フレームワーク、使い方、implement、setup、インストール
→ **Task with subagent_type: research-library**

#### Pattern 2: Codebase Analysis  
キーワード: 構造、実装を理解、どこに定義、依存関係、アーキテクチャ、分析
→ **Task with subagent_type: research-codebase**

#### Pattern 3: Error Resolution
キーワード: エラー、動かない、失敗、issue、bug、fix、TypeError、解決
→ **Task with subagent_type: research-error**

#### Pattern 4: API Specification
キーワード: API、仕様、specification、interface、endpoint、schema、REST
→ **Task with subagent_type: research-api**

#### Pattern 5: Latest Trends
キーワード: 最新、trend、2024、2025、best practice、新機能、動向
→ **Task with subagent_type: research-trends**

#### Pattern 6: Domain-Specific Research
キーワード: ドメインのみ、特定サイト、include_domains、サイト限定
→ **Task with subagent_type: research-domain**

#### Pattern 7: Time-Sensitive News
キーワード: ニュース、news、過去X日、最近、今週、今月
→ **Task with subagent_type: research-news**

### 実行フロー

1. **パターン判定**: 上記キーワードから最適なパターンを選択
2. **エージェント起動**: Task toolで専門エージェントを実行
3. **調査実行**: 選択されたエージェントが詳細調査を実施
4. **結果報告**: Evidence-basedな調査結果を提供

### 複数パターンに該当する場合

複数のパターンに該当する場合は、最も重要度の高いパターンを優先：
1. Error Resolution（問題解決優先）
2. API Specification（仕様確認）
3. Library/Framework（実装方法）
4. Latest Trends（最新情報）
5. その他

### エラーハンドリング

エージェント実行時にエラーが発生した場合：
1. エラー内容を分析
2. 代替パターンを選択
3. 必要に応じて手動調査にフォールバック

## 期待される成果

- 包括的で正確な調査結果
- 複数の情報源からの検証済み情報
- 実装可能な具体的提案
- Evidence-basedな報告書