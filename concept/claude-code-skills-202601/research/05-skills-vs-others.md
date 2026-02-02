# Skills vs Commands vs Rules vs Subagents

## 概要

Claude Codeをカスタマイズする4つの主要な仕組み（Rules、Commands、Skills、Subagents）の違いと、SkillsにおけるTask ContentsとReference Contentsの使い分けについてまとめる。

---

## 4つの拡張方法の比較

| 機能 | 配置場所 | 呼び出し | コンテキスト | 主な用途 |
|------|---------|---------|------------|---------|
| **Rules** | `.claude/rules/` | 常時適用 | メインコンテキスト | プロジェクト全体の不変ルール |
| **Commands** | `.claude/commands/` | 手動（`/command`） | メインコンテキスト | ユーザー起動のショートカット |
| **Skills** | `.claude/skills/` | 自動/手動両対応 | メイン or フォーク | 再利用可能な専門知識・タスク |
| **Subagents** | `.claude/agents/` | 委譲時 | 独立コンテキスト | 複雑なマルチステップタスク |

---

## 各方法の詳細

### Rules（.claude/rules/）

**定義**:
- プロジェクト全体に適用される不変のルールと制約
- Claudeが常に意識すべきコンテキスト

**特徴**:
- セッション全体で常時ロードされる
- オーバーライド不可
- プロジェクトの「憲法」のような存在

**ユースケース**:
- コーディング規約
- プロジェクトの基本方針
- 禁止事項（「本番DBに直接接続しない」等）

---

### Commands（.claude/commands/）

**定義**:
- ユーザーが `/command` で明示的に起動するショートカット
- 保存された再利用可能なプロンプト

**特徴**:
- 手動呼び出しのみ
- シンプルなMarkdownファイル
- v2.1.3以降、Skillsと統合（`.claude/commands/` のファイルもSkillsとして機能）

**ユースケース**:
- 繰り返し使うワークフロー
- テンプレート化された作業

**注意**:
> **2026年1月以降、CommandsとSkillsは実質的に統合された。**
> `.claude/commands/review.md` と `.claude/skills/review/SKILL.md` は同じように `/review` を作成し、同じように動作する。
> 既存の `.claude/commands/` ファイルはそのまま動作するが、新規作成時はSkills形式が推奨される。

---

### Skills（.claude/skills/）

**定義**:
- 自動発見可能で、手動呼び出しもできる柔軟な専門知識・タスク定義
- Markdownファイル + オプションのサポートファイル

**特徴**:
- **ポータブル**: Claude.ai / Claude Code / API で共通フォーマット
- **自動発見**: Claudeが `description` に基づいて関連時に自動ロード
- **手動呼び出し**: `/skill-name` で明示的に起動可能
- **フォーク可能**: `context: fork` で独立コンテキストで実行
- **サポートファイル**: スクリプト、リファレンス、テンプレート等を含められる

**ユースケース**:
- ドメイン固有の専門知識（Reference Content）
- 再利用可能なワークフロー（Task Content）
- プロジェクト固有のツール

---

### Subagents（.claude/agents/）

**定義**:
- 特定のタスクタイプに特化したAIアシスタント
- 独立したコンテキストウィンドウで動作

**特徴**:
- **独立コンテキスト**: メイン会話履歴から分離
- **カスタムシステムプロンプト**: エージェント固有の指示
- **特定ツールアクセス**: 必要なツールのみ許可
- **マルチステップ実行**: 複雑なタスクを分離して処理

**ユースケース**:
- コードベース探索（Explore agent）
- 実装計画立案（Plan agent）
- 独立した調査タスク

---

## 2026年の収束トレンド

### CommandsとSkillsの統合（v2.1.3）

> **Merged slash commands and skills, simplifying the mental model with no change in behavior.**
> - Claude Code CHANGELOG v2.1.3

- `.claude/commands/` のファイルと `.claude/skills/` のファイルが同じ動作
- 同名の場合、Skillsが優先
- メンタルモデルの簡略化

### SkillsとSubagentsの接近

**Skillsが `context: fork` でSubagentとして実行可能に**:

```yaml
---
name: my-skill
context: fork
agent: Explore
---

タスク内容...
```

- Skillsの内容がサブエージェントのプロンプトになる
- エージェントタイプを指定可能

**Subagentsがskillsフィールドでスキルをプリロード可能に**:

```yaml
---
name: my-agent
skills:
  - api-conventions
  - testing-guide
---

エージェントの指示...
```

### 使い分けの指針

| 用途 | 推奨方法 | 理由 |
|------|---------|------|
| 不変のルール | Rules (`.claude/rules/`) | 常時適用、オーバーライド不可 |
| 専門知識（自動適用） | Skills（Reference Content） | ポータブル、自動発見 |
| ワークフロー（手動実行） | Skills（Task Content） | フォーク可能、サポートファイル |
| 複雑なマルチステップ | Subagents | 独立コンテキスト、フォーカス維持 |

---

## Skills内の2つのコンテンツタイプ

### Reference Content - 知識をインラインで適用

**目的**: Claudeの現在の作業に専門知識を組み込む

**特徴**:
- 慣例、パターン、スタイルガイド、ドメイン知識
- インラインで実行（メインコンテキストに統合）
- 自動発見を前提（`disable-model-invocation: false`）
- `user-invocable: false` でメニューから非表示可能

**フロントマター例**:
```yaml
---
name: api-conventions
description: このコードベースのAPI設計パターン
# disable-model-invocation: false（デフォルト）
user-invocable: false  # スラッシュメニューに表示しない
---
```

**ユースケース**:
1. **コーディング規約**
   ```yaml
   ---
   name: style-guide
   description: プロジェクトのコーディングスタイル
   ---

   - 変数名はcamelCase
   - 関数は純粋関数を優先
   - 副作用は明示的に
   ```

2. **アーキテクチャパターン**
   ```yaml
   ---
   name: architecture-patterns
   description: このプロジェクトのアーキテクチャパターン
   ---

   ## レイヤー構造
   - Presentation Layer (React components)
   - Application Layer (hooks, services)
   - Domain Layer (business logic)
   ```

3. **レガシーシステムコンテキスト**
   ```yaml
   ---
   name: legacy-system-context
   description: レガシーシステムの仕様と制約
   user-invocable: false
   ---

   旧システムとの互換性を保つため:
   - 日付フォーマットはYYYYMMDD固定
   - 文字コードはShift-JIS
   ```

4. **ドメイン知識**
   ```yaml
   ---
   name: domain-glossary
   description: 業務用語集
   ---

   ## 用語定義
   - **締め処理**: 月末に行う売上確定処理
   - **仮締め**: 日次の暫定集計
   ```

---

### Task Content - ステップバイステップの作業指示

**目的**: 具体的なアクションの実行

**特徴**:
- デプロイ、コミット、コード生成などの具体的タスク
- 通常は `/skill-name` で手動呼び出し
- `disable-model-invocation: true` で自動実行を防ぐ
- `context: fork` でサブエージェントとして実行推奨

**フロントマター例**:
```yaml
---
name: deploy
description: 本番環境へのデプロイ
disable-model-invocation: true  # 自動実行を防ぐ
context: fork                   # 独立コンテキストで実行
agent: general-purpose
---
```

**ユースケース**:
1. **デプロイワークフロー**
   ```yaml
   ---
   name: deploy
   description: 本番環境へのデプロイ
   disable-model-invocation: true
   context: fork
   ---

   アプリケーションを本番環境にデプロイ:
   1. テストスイートを実行
   2. ビルドを作成
   3. ステージング環境で検証
   4. 本番環境にデプロイ
   5. ヘルスチェック実行
   ```

2. **Git操作**
   ```yaml
   ---
   name: create-pr
   description: プルリクエストを作成
   disable-model-invocation: true
   ---

   現在のブランチからPRを作成:
   1. コミットログから変更内容を分析
   2. PRタイトルと説明を生成
   3. gh pr create で作成
   4. レビュアーをアサイン
   ```

3. **テスト実行と分析**
   ```yaml
   ---
   name: test-and-analyze
   description: テストを実行して結果を分析
   disable-model-invocation: true
   context: fork
   ---

   テストを実行して失敗を分析:
   1. npm test を実行
   2. 失敗したテストを特定
   3. 関連コードを読む
   4. 修正案を提示
   ```

4. **コード生成**
   ```yaml
   ---
   name: generate-api-client
   description: OpenAPI仕様からAPIクライアントを生成
   disable-model-invocation: true
   ---

   OpenAPI仕様 $ARGUMENTS からクライアントを生成:
   1. 仕様を読み込む
   2. TypeScript型定義を生成
   3. APIクライアント関数を生成
   4. テストコードを生成
   ```

---

## 使い分けの決定フローチャート

```
Claudeの振る舞いをカスタマイズしたい
    ↓
[常に適用されるべきルール？]
    YES → Rules (.claude/rules/)
    NO  ↓
[Claudeが自動で適用すべき知識？]
    YES → Skills (Reference Content)
          - user-invocable: false
          - サポートファイルで詳細化
    NO  ↓
[ユーザーが起動する具体的なタスク？]
    YES → Skills (Task Content)
          - disable-model-invocation: true
          - context: fork推奨
    NO  ↓
[複雑なマルチステップタスク？]
    YES → Subagents
          - 独立コンテキスト
          - カスタムシステムプロンプト
```

---

## ベストプラクティス

### Reference Content作成時
1. **サイズ管理**
   - SKILL.mdは500行以下に
   - 詳細は別ファイル（reference.md等）に分離
   - SKILL.mdから `[reference.md](reference.md)` で参照

2. **自動発見の最適化**
   - `description` に具体的なキーワードを含める
   - ユーザーが自然に言いそうな表現を使う

3. **メニュー表示**
   - 背景知識的なSkillsは `user-invocable: false` で非表示に
   - アクションとして意味のあるものだけメニューに

### Task Content作成時
1. **自動実行の防止**
   - 副作用のあるタスクは必ず `disable-model-invocation: true`
   - デプロイ、コミット、外部API呼び出し等

2. **独立実行**
   - `context: fork` で独立コンテキストで実行
   - メイン会話と混ざらない
   - `agent` フィールドで適切なエージェントタイプを指定

3. **引数の活用**
   - `$ARGUMENTS` や `$0`, `$1` で柔軟な入力対応
   - `argument-hint` でオートコンプリート時のヒント表示

---

## 出典

- [Claude Code公式: Extend Claude with skills](https://code.claude.com/docs/en/skills)
- [Claude公式: Skills explained](https://claude.com/blog/skills-explained)
- [Builder.io: Agent Skills vs. Rules vs. Commands](https://www.builder.io/blog/agent-skills-rules-commands)
- [Understanding Claude Code: Skills vs Commands vs Subagents](https://www.youngleaders.tech/p/claude-skills-commands-subagents-plugins)
- [Claude Skills vs Subagent: What's the difference?](https://www.eesel.ai/blog/skills-vs-subagent)
- [Subagents, Commands and Skills Are Converging](https://www.vivekhaldar.com/articles/claude-code-subagents-commands-skills-converging/)
