# E2Eテスト環境のフォルダ構造（emrum準拠）

## 概要

このドキュメントは、Claude Codeでのオンボーディングカスタムコマンド（`/onboard-03-init-e2e`）が生成するE2Eテスト環境のフォルダ構造を定義します。

**重要**: この構造は[emrum01/chrome-devtools-e2e-sample](https://github.com/emrum01/chrome-devtools-e2e-sample)に準拠し、**MCP Playwright + AI主導方式**を採用しています。

## 参考資料

- **リポジトリ**: [emrum01/chrome-devtools-e2e-sample](https://github.com/emrum01/chrome-devtools-e2e-sample)
- **技術記事**: [AI主導のE2Eテスト - Claude Code × Chrome DevTools MCP](https://zenn.dev/emrum/articles/ai-driven-e2e-claude-code-chrome-devtools-mcp)

---

## フォルダ構造

```
project-root/
├── docs/
│   └── e2e-testing/                    # E2Eテスト環境のルート
│       ├── RUNBOOK.md                  # AI実行手順書（最重要）
│       ├── OVERVIEW.md                 # E2Eテストの概要説明
│       │
│       ├── guidelines/                 # AIルール・環境固有情報
│       │   ├── ai-rules.md             # 環境共通のAI実行ルール
│       │   ├── local-env.md            # ローカル環境固有情報
│       │   └── staging-env.md          # ステージング環境固有情報
│       │
│       ├── features/                   # 機能別テストケース（Gherkin）
│       │   ├── 認証.feature            # @タグで環境制御
│       │   └── *.feature               # プロジェクト固有機能
│       │
│       ├── scenarios/                  # エンドツーエンドシナリオ
│       │   └── *.feature               # @タグで環境制御
│       │
│       ├── snippets/                   # フォールバックスクリプト
│       │   ├── wait-for-element.js     # 環境共通
│       │   └── *.js                    # 必要に応じて追加
│       │
│       ├── fixtures/                   # テストフィクスチャ
│       │   ├── test-users-local.json   # ローカル環境用データ
│       │   ├── test-users-staging.json # ステージング環境用データ
│       │   └── mock-data.json          # 共通データ
│       │
│       └── results/                    # テスト結果（.gitignore対象）
│           ├── local/                  # ローカル実行結果
│           │   ├── screenshots/
│           │   └── logs/
│           └── staging/                # ステージング実行結果
│               ├── screenshots/
│               └── logs/
```

---

## 各ディレクトリの役割

### 1. `docs/e2e-testing/` - ルートディレクトリ

**配置理由**: emrum構造に準拠。テストドキュメントはdocs/配下に配置。

**重要ファイル**:
- `RUNBOOK.md`: AIが読み込んで実行手順を理解する最重要ファイル
- `OVERVIEW.md`: E2Eテスト環境の概要説明

### 2. `guidelines/` - AIルール・環境固有情報

**環境共通ファイル**:
- `ai-rules.md`: MCP Playwright操作の基本ルール、要素選択優先順位、待機戦略、@タグと環境マッピング

**環境固有ファイル**:
- `local-env.md`: baseURL、テストユーザー情報、DBリセット手順
- `staging-env.md`: baseURL、認証トークン取得、データクリーンアップ手順

### 3. `features/` - 機能別テストケース

**特徴**:
- Gherkin形式で自然言語記述
- `@local`、`@staging`、`@smoke`タグで環境制御
- **環境別サブディレクトリなし**（emrum準拠）

**例**:
```gherkin
# language: ja
フィーチャ: ユーザー認証

  @local @staging @smoke
  シナリオ: 正常なログイン
    前提 ログインページを開く
    もし メールアドレス "test@example.com" を入力
    かつ パスワード "password123" を入力
    かつ ログインボタンをクリック
    ならば ダッシュボードが表示される
```

### 4. `scenarios/` - エンドツーエンドシナリオ

**特徴**:
- 複数機能を統合したシナリオ
- @タグで環境制御
- **環境別サブディレクトリなし**（emrum準拠）

### 5. `snippets/` - フォールバックスクリプト

**特徴**:
- 環境共通のJavaScriptファイル
- MCP Playwright操作が失敗した場合のフォールバック
- `browser_evaluate`で実行

**例**:
```javascript
// wait-for-element.js
async function waitForElement(page, selector, options = {}) {
  const timeout = options.timeout || 5000;
  await page.waitForSelector(selector, { timeout });
  return page.locator(selector);
}
```

### 6. `fixtures/` - テストフィクスチャ

**特徴**:
- **環境別ファイル名**で管理（サブディレクトリなし）
- ファイル命名規則: `{データ種別}-{環境}.json`

**例**:
```json
// test-users-local.json
{
  "admin": {
    "email": "admin@localhost",
    "password": "admin123"
  }
}
```

### 7. `results/` - テスト結果

**特徴**:
- `.gitignore`対象
- **環境別サブディレクトリあり**（local/、staging/）
- スクリーンショット、ログを保存

---

## emrum構造との主要な相違点（従来の誤った設計との比較）

| 項目 | emrum構造（正） | 誤った設計（旧） |
|------|----------------|-----------------|
| **配置場所** | `docs/e2e-testing/` | `e2e/` |
| **環境分離** | @タグ + 環境別ファイル | 環境別サブディレクトリ |
| **features/** | フラット構造 | `local/`, `staging/`, `common/` |
| **scenarios/** | フラット構造 | `local/`, `staging/`, `common/` |
| **snippets/** | フラット構造 | `local/`, `staging/`, `common/` |
| **fixtures/** | 環境別ファイル名 | `local/`, `staging/`, `common/` |
| **guidelines/** | `ai-rules.md`, `local-env.md`, `staging-env.md` | `ai-rules.md`, `local-rules.md`, `staging-rules.md` |
| **config/** | なし（不要） | `local.config.ts`, `staging.config.ts` |
| **tests/** | なし（不要） | `*.spec.ts`ファイル |

---

## 環境制御の方法

### 1. @タグによる制御（features/、scenarios/）

```gherkin
@local          # ローカル環境のみ
@staging        # ステージング環境のみ
@local @staging # 両環境で実行
@smoke          # スモークテスト（全環境）
```

### 2. 環境別ファイルによる制御（fixtures/）

```
test-users-local.json    # ローカル環境用
test-users-staging.json  # ステージング環境用
mock-data.json           # 共通データ
```

### 3. 環境別ガイドラインによる制御（guidelines/）

```
ai-rules.md      # 環境共通ルール
local-env.md     # ローカル環境固有情報（baseURL等）
staging-env.md   # ステージング環境固有情報（baseURL等）
```

---

## MCP Playwright + AI主導方式の特徴

### 従来のPlaywright自動化との違い

| 項目 | MCP Playwright + AI主導 | 従来のPlaywright |
|------|------------------------|-----------------|
| **テストコード** | 不要（Gherkinのみ） | .spec.tsファイル必須 |
| **実行方法** | AIがRUNBOOKを読んで自律的に操作 | `npm run e2e`で事前定義スクリプト実行 |
| **設定ファイル** | 不要 | `playwright.config.ts`必須 |
| **ブラウザ操作** | MCP Playwrightツール | Playwright API |
| **柔軟性** | AIが自然言語を解釈して柔軟に対応 | 固定されたテストスクリプト |

### AI主導E2Eテストの実行フロー

```
1. ユーザーが /e2e-local を実行
   ↓
2. AIがRUNBOOK.mdを読み込み
   ↓
3. AIがguidelines/（ai-rules.md、local-env.md）を読み込み
   ↓
4. AIがSerena分析結果から機能一覧を取得
   ↓
5. AIがユーザーに確認プロンプト表示
   「アプリケーション名」
   「機能一覧: A, B, C」
   「ログイン認証〜上記の機能確認を行います。よろしいですか？ y/n」
   ↓
6. ユーザーが y と応答
   ↓
7. AIがMCP Playwrightでブラウザを自律的に操作
   - browser_navigate でページ遷移
   - browser_type でテキスト入力
   - browser_click でボタンクリック
   - browser_snapshot で要素確認
   ↓
8. AIがテスト結果をチャットで報告
```

---

## 生成されないもの（MCP Playwright + AI主導方式では不要）

- `playwright.config.ts` - 設定ファイル不要
- `*.spec.ts` - テストコード不要
- 環境別サブディレクトリ（features/local/等） - @タグで制御

---

## .gitignoreへの追加

```gitignore
# E2Eテスト結果
docs/e2e-testing/results/
*.webm
*.mp4
```

---

## 生成コマンド

```bash
# E2E環境初期化（フォルダ構造自動生成）
/onboard-03-init-e2e

# ローカルE2Eテスト実行
/e2e-local

# ステージングE2Eテスト実行
/e2e-staging [ステージングURL]
```

---

## まとめ

この構造は以下の原則に基づいています：

1. **emrum準拠**: 実証済みの構造を採用
2. **フラット構造**: 環境別サブディレクトリを作らず、@タグと環境別ファイルで制御
3. **AI主導**: Gherkin形式の自然言語シナリオをAIが解釈して実行
4. **MCP統合**: MCP Playwrightツールでブラウザ操作
5. **シンプル**: 不要なconfig/やtests/ディレクトリを排除

この構造により、プロジェクトの性質に応じた柔軟なE2Eテスト環境を自動生成できます。
