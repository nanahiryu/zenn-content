# Skills誕生の背景と時系列の補足調査

## 概要

concept.mdで「要リサーチ」とマークされていた項目について、公式情報を調査しまとめる。

---

## 1. Skillsが作られた理由（公式見解）

### 背景にあった課題

> **As model capabilities improve, we can now build general-purpose agents that interact with full-fledged computing environments, but as these agents become more powerful, we need more composable, scalable, and portable ways to equip them with domain-specific expertise.**

Anthropic公式ブログより、以下の問題意識が明らかになった：

1. **モデル能力の向上**: 汎用エージェントが本格的な計算環境と対話できるようになった
2. **専門知識の付与**: より強力になるエージェントに、composable（組み合わせ可能）、scalable（拡張可能）、portable（ポータブル）な方法でドメイン固有の専門知識を与える必要があった

### 繰り返しコンテキスト設定の問題

> **Skills are Anthropic's answer to the problem of repetitive context-setting - instead of treating Claude as a blank slate every time, you can package your expertise, workflows, and domain knowledge into reusable modules that Claude automatically discovers and applies when relevant.**

- Claudeを毎回白紙の状態として扱うのではなく、専門知識・ワークフロー・ドメイン知識を再利用可能なモジュールにパッケージ化
- Claudeが関連する時に自動的に発見・適用する

### プロンプトとの違い

> **Unlike prompts (conversation-level instructions for one-off tasks), Skills load on-demand and eliminate the need to repeatedly provide the same guidance across multiple conversations.**

- プロンプト: 1回限りのタスク向け会話レベルの指示
- Skills: オンデマンドでロード、複数の会話で同じガイダンスを繰り返し提供する必要を排除

---

## 2. 正確なリリース日

### ⚠️ 重要な訂正: Phase 2は2026年1月

concept.mdでは「2025年末〜」としていたが、実際のリリース日は以下：

| バージョン | リリース日 | 備考 |
|-----------|-----------|------|
| Skills初期リリース | 2025年10月16日 | Claude.ai、Claude Code、APIの3プラットフォーム同時対応 |
| Skillsオープンスタンダード化 | 2025年12月18日 | クロスプラットフォームポータビリティのためオープン標準として公開 |
| **v2.1.0** | **2026年1月7日** | ホットリロード、context: fork、agentフィールド、フック機能、once: true等 |
| **v2.1.3** | **2026年1月9日** | スラッシュコマンド統合（メンタルモデル簡略化） |
| v2.1.12 | 2026年2月頃 | 調査時点の最新版 |

### Phase分けの修正提案

concept.mdの「Phase 2: 開発体験の改善（2025年末〜）」は**誤り**。正しくは：

- **Phase 2: 開発体験の改善（2026年1月〜）**
  - v2.1.0（2026年1月7日）: ホットリロード、context: fork、agentフィールド、フック機能、once: true
  - v2.1.3（2026年1月9日）: スラッシュコマンド統合

「Phase 3: 高度なカスタマイズ（2026年〜）」としていた機能は、実際にはv2.1.0に含まれているため、**Phase 2と統合すべき**。

---

## 3. 初期フロントマター仕様

### 必須フィールド

- **name**: スキルの人間が読みやすい名前（64文字以内）
- **description**: Claudeがスキルをいつ呼び出すかを判断するための説明（200文字以内）

### 進化してきた機能

調査では初期バージョン番号は特定できなかったが、以下の機能が段階的に追加されてきた：

| 機能 | 説明 |
|------|------|
| `user-invocable: false` | スラッシュコマンドメニューから隠す（デフォルトはtrue） |
| `disable-model-invocation: true` | モデルによる自動呼び出しを無効化 |
| `allowed-tools` | 利用可能なツールのリスト（YAML形式対応） |
| `permissions` | 必要な権限のリスト |
| `context: fork` | サブエージェントとして実行 |
| `agent` | エージェントタイプ指定 |
| `once: true` | 一度だけ実行 |
| `hooks` | PreToolUse、PostToolUse、Setup、Stop |

---

## 4. タイムラインまとめ

Skills誕生から現在（2026年2月）まで：

```
2025年10月16日: Skills発表
    ↓ (約2ヶ月)
2025年12月18日: オープンスタンダード化
    ↓ (約3週間)
2026年1月7日: v2.1.0リリース（大型アップデート）
    ↓ (2日)
2026年1月9日: v2.1.3リリース（スラッシュコマンド統合）
    ↓ (約3週間、細かなアップデート継続)
2026年2月: v2.1.12（最新）
```

**実際の期間**: 約3ヶ月半（2025年10月→2026年2月）

concept.mdの「約4ヶ月」は概ね正確だが、「Phase 2が2025年末」という記述は誤り。

---

## 出典

- [Equipping agents for the real world with Agent Skills - Anthropic Engineering](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
- [Introducing Agent Skills - Anthropic](https://www.anthropic.com/news/skills)
- [Claude Code GitHub Releases](https://github.com/anthropics/claude-code/releases)
- [Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
- [Claude Code 2.1.0 arrives with smoother workflows and smarter agents - VentureBeat](https://venturebeat.com/orchestration/claude-code-2-1-0-arrives-with-smoother-workflows-and-smarter-agents)
- [Extend Claude with skills - Claude Code Docs](https://code.claude.com/docs/en/skills)
- [Anthropic Launches Skills Open Standard for Claude - AI Business](https://aibusiness.com/foundation-models/anthropic-launches-skills-open-standard-claude)
- [How to Create Claude Code Skills: The Complete Guide from Anthropic](https://websearchapi.ai/blog/how-to-create-claude-code-skills)
