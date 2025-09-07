# Claude Global Commands

Claude Codeで使用できる高度なカスタムコマンド集です。

## 📚 セットアップガイド

詳細なセットアップ方法とMCPツールの設定については、以下の記事を参照してください：

**[Claude Code カスタムコマンド完全ガイド](https://note.com/y__u777/n/na5927199b0ac)**

この記事では以下について詳しく解説しています：
- MCPサーバーの設定方法
- 各種ツールのインストール手順
- トラブルシューティング

## 🚀 クイックスタート

1. このリポジトリをクローン
```bash
git clone https://github.com/y__u/claude-global-commands.git
```

2. `~/.claude/`ディレクトリにファイルをコピー
```bash
cd claude-global-commands
cp -r commands ~/.claude/
cp -r agents ~/.claude/
```

3. 必要に応じてMCPツールを設定（上記記事参照）

## 📦 含まれるコマンド

- `/commit` - 変更内容をAIが要約して自動コミット
- `/meta` - セッション引き継ぎ用メタプロンプト生成
- `/pr` - PR自動作成（Mermaid図付き）
- `/research` - 包括的調査（専門エージェント振り分け）
- `/think` - 高度な分析と計画

## 🔧 含まれるサブエージェント

### リサーチ系エージェント
- `research-api` - API仕様とドキュメント調査
- `research-codebase` - コードベース構造分析
- `research-domain` - 特定ドメインの情報調査
- `research-error` - エラー解決策の調査
- `research-library` - ライブラリ・フレームワーク調査
- `research-news` - 時事ニュースと発表の調査
- `research-trends` - 最新トレンドとベストプラクティス調査

### 思考系エージェント
- `think-analyzer` - 深い分析と問題理解専門
- `think-planner` - 実装計画と戦略立案専門
- `think-solver` - 実践的問題解決専門

## ⚙️ MCPツール設定（オプション）

一部のコマンドはMCPツールを使用しますが、必須ではありません。設定方法は[セットアップガイド](https://note.com/y__u777/n/na5927199b0ac)を参照してください。

使用されるMCPツール：
- serena（コードベース分析）
- context7（ドキュメント取得）
- tavily（Web検索）
- deepwiki（GitHubドキュメント）
- sequential-thinking（思考支援）

## 📄 ライセンス

MIT License - 詳細は[LICENSE](./LICENSE)を参照

## 🤝 コントリビューション

Issues、Pull Requestsを歓迎します。

---

Claude Code用カスタムコマンド集 by y__u