# Agent Skills 設計思想

## 概要

Anthropicが発表したAgent Skillsの設計原則と技術的特徴をまとめる。

---

## 4つの設計原則

### 1. Composable（組み合わせ可能）
- 複数のスキルが連携して動作可能
- 単一タスクに複数スキルを適用できる

### 2. Portable（ポータブル）
> Skills are portable—they use the same format everywhere, allowing you to build once and use across Claude apps, Claude Code, and API.

- 同一フォーマットがすべてのプラットフォームで動作
- Claude apps / Claude Code / API で共通
- "build once, use everywhere"

### 3. Efficient（効率的）
> Claude will only access a skill when it's relevant to the task at hand.

- 必要な時だけスキルをロード
- 処理速度を維持
- コンテキストウィンドウの効率的な使用

### 4. Powerful（強力）
> Skills can include executable code for tasks where traditional programming is more reliable than token generation.

- 実行可能コードを含められる
- トークン生成より信頼性が必要な場面で活用
- スクリプト・リソースの同梱

---

## 技術的構造

### スキルの構成要素

```
skill-name/
├── skill-name.md      # メインの指示文（フロントマター含む）
├── scripts/           # 実行可能スクリプト（オプション）
└── resources/         # 追加リソース（オプション）
```

### フロントマター

```yaml
---
description: スキルの短い説明
allowed-tools:
  - Bash
  - Read
  - Write
permissions:
  - "run npm commands"
context: fork        # サブエージェントとして実行
agent: Explore       # エージェントタイプ指定
once: true           # 一度だけ実行
hooks:
  - PreToolUse
  - PostToolUse
---
```

### 配置場所

- `~/.claude/skills/` - グローバルスキル
- `.claude/skills/` - プロジェクトスキル
- サブディレクトリの `.claude/skills/` も自動検出される

---

## MCPとの違い

| 観点 | MCP | Skills |
|------|-----|--------|
| 提供するもの | ツール（機能） | 振る舞い（ワークフロー） |
| 形式 | サーバー実装 | Markdownファイル |
| 複雑さ | 実装が必要 | テキスト記述のみ |
| 用途 | 外部システム連携 | AIの行動パターン定義 |

> MCPがツールを提供するなら、Skillsは「振る舞い」を提供する
> - Simon Willison

---

## オープン標準

AnthropicはAgent Skillsをオープン標準として公開：
- 他のAIプラットフォームでも採用可能
- エコシステムの形成を促進
- パートナー製スキルのディレクトリも提供

---

## 出典

- [Anthropic Engineering: Equipping agents for the real world with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
- [Anthropic公式: Introducing Agent Skills](https://claude.com/blog/skills)
- [Simon Willison: Claude Skills are awesome, maybe a bigger deal than MCP](https://simonwillison.net/2025/Oct/16/claude-skills/)
