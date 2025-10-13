---
description: リポジトリクローン〜ローカル環境構築＋AIDD-Knowledge環境準備
allowed-tools: ["Bash", "Read", "Write", "Glob", "TodoWrite"]
argument-hint: [リポジトリURL]
---

# Phase 1: 環境検証

## 必須ツールの確認

### git --version実行
!echo "=== 必須ツールのバージョン確認 ==="
!git --version

### node --version実行
!node --version

### npm --version実行
!npm --version

## 環境変数の確認

### HOME確認
!echo "HOME: $HOME"

### PWD確認
!echo "PWD: $PWD"

## ディスク容量の確認

### df -h実行
!df -h

# Phase 2: AIDD-Knowledge環境の確認・初期化

## AIDD-Knowledgeディレクトリの確認

### ~/AIDD-Knowledge/存在確認
!if [ ! -d ~/AIDD-Knowledge ]; then
!  echo "❌ ~/AIDD-Knowledgeが見つかりません"
!  echo ""
!  echo "📋 以下のいずれかの方法でAIDD-Knowledge環境を準備してください:"
!  echo ""
!  echo "【方法1】engineering-knowledgeリポジトリをクローン済みの場合:"
!  echo "  cp -r ~/github-public/engineering-knowledge ~/AIDD-Knowledge"
!  echo ""
!  echo "【方法2】engineering-knowledgeリポジトリをクローンする場合:"
!  echo "  git clone https://github.com/user/engineering-knowledge ~/github-public/engineering-knowledge"
!  echo "  cp -r ~/github-public/engineering-knowledge ~/AIDD-Knowledge"
!  echo ""
!  echo "【方法3】新規に作成する場合:"
!  echo "  mkdir -p ~/AIDD-Knowledge/projects/template"
!  echo "  # 必要なフォルダ構造を作成してください"
!  echo ""
!  exit 1
!else
!  echo "✅ AIDD-Knowledge環境を確認しました"
!fi

# Phase 3: リポジトリクローン（~/Projects/固定）

## ~/Projects/ディレクトリの確認

### ~/Projects/存在確認
!if [ ! -d ~/Projects ]; then
!  echo "❌ ~/Projectsが見つかりません"
!  echo ""
!  echo "📋 以下のコマンドで作業用ディレクトリを作成してください:"
!  echo "  mkdir -p ~/Projects"
!  echo ""
!  echo "💡 ~/Projects/はプロジェクト管理用のディレクトリです"
!  echo ""
!  exit 1
!else
!  echo "✅ ~/Projectsディレクトリを確認しました"
!fi

## クローン実行

### リポジトリURL検証
!REPO_URL="$ARGUMENTS"
!REPO_NAME=$(basename "$REPO_URL" .git)
!echo "リポジトリURL: $REPO_URL"
!echo "リポジトリ名: $REPO_NAME"

### ~/Projects/へgit clone実行
!cd ~/Projects
!if [ -d "$REPO_NAME" ]; then
!  echo "⚠️ ~/Projects/$REPO_NAME は既に存在します"
!  cd "$REPO_NAME"
!else
!  git clone "$REPO_URL"
!  cd "$REPO_NAME"
!  echo "✅ ~/Projects/$REPO_NAME にクローンしました"
!fi

## ブランチ確認

### git branch -a実行
!git branch -a

### git status実行
!git status

# Phase 4: プロジェクト専用ナレッジフォルダの作成

## AIDD-Knowledge/projects/配下にプロジェクトフォルダ作成

### ~/AIDD-Knowledge/projects/${REPO_NAME}/作成
!if [ ! -d ~/AIDD-Knowledge/projects/"$REPO_NAME" ]; then
!  echo "📂 プロジェクト専用ナレッジフォルダを作成中..."
!  mkdir -p ~/AIDD-Knowledge/projects/"$REPO_NAME"
!  cp -r ~/AIDD-Knowledge/projects/template/* ~/AIDD-Knowledge/projects/"$REPO_NAME"/
!  echo "✅ ~/AIDD-Knowledge/projects/$REPO_NAME を作成しました"
!else
!  echo "⚠️ ~/AIDD-Knowledge/projects/$REPO_NAME は既に存在します"
!fi

# Phase 5: 依存関係のインストール

## package.jsonの確認

### ファイル存在確認
!if [ -f "package.json" ]; then
!  echo "✅ package.jsonが見つかりました"
!  echo "--- package.json の内容（最初の20行） ---"
!  head -20 package.json
!  echo "---"
!else
!  echo "⚠️ package.jsonが見つかりません（Node.jsプロジェクトでない可能性）"
!fi

## npmインストール実行

### npm install実行
!if [ -f "package.json" ]; then
!  echo "📦 依存関係をインストール中..."
!  npm install
!else
!  echo "⏭️ Node.jsプロジェクトでないため、npm installをスキップします"
!fi

## インストール結果の検証

### node_modules確認
!if [ -d "node_modules" ]; then
!  echo "✅ node_modules が作成されました"
!  du -sh node_modules
!fi

# Phase 6: 設定ファイルの準備

## 設定ファイルの確認

### .env.example検出
!if [ -f ".env.example" ]; then
!  echo "✅ .env.exampleが見つかりました"
!fi

### .envファイル作成
!if [ -f ".env.example" ] && [ ! -f ".env" ]; then
!  cp .env.example .env
!  echo "✅ .envファイルを作成しました"
!elif [ -f ".env" ]; then
!  echo "⚠️ .envファイルは既に存在します"
!else
!  echo "⏭️ .env.exampleが存在しないため、.env作成をスキップします"
!fi

## その他の設定ファイル確認

### 設定ファイル一覧表示
!echo "--- 設定ファイル一覧 ---"
!ls -la | grep -E '\.(json|yaml|yml|toml|ini|config)$' || echo "設定ファイルなし"

# Phase 7: ビルドとテスト実行可能性の確認

## ビルドスクリプトの確認

### package.jsonスクリプト確認
!if [ -f "package.json" ]; then
!  if grep -q '"build"' package.json; then
!    echo "✅ buildスクリプトが見つかりました"
!  else
!    echo "⚠️ buildスクリプトが見つかりません"
!  fi
!fi

## ビルドの試行

### npm run build実行
!if [ -f "package.json" ] && grep -q '"build"' package.json; then
!  echo "🔨 ビルドを実行中..."
!  npm run build
!else
!  echo "⏭️ buildスクリプトが存在しないため、ビルドをスキップします"
!fi

# Phase 8: セットアップ完了報告

## 完了サマリー

### プロジェクトクローン先表示
!echo ""
!echo "🎉 セットアップが完了しました"
!echo ""
!echo "📂 プロジェクトクローン先: ~/Projects/$REPO_NAME"

### ナレッジフォルダ保存先表示
!echo "📚 ナレッジフォルダ: ~/AIDD-Knowledge/projects/$REPO_NAME"

### 環境情報表示
!echo ""
!echo "🔧 環境情報:"
!echo "  - Git: $(git --version | head -1)"
!echo "  - Node: $(node --version 2>/dev/null || echo '未インストール')"
!echo "  - npm: $(npm --version 2>/dev/null || echo '未インストール')"

## 次のステップ案内

### /onboard-02-serena-analyze案内
!echo ""
!echo "📝 次のステップ:"
!echo "  /onboard-02-serena-analyze でプロジェクト構造を分析"
