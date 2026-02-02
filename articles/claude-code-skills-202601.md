---
title: "2026/01のClaude Code Skillsについてまとめる"
emoji: "🛠️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["claudecode", "claude", "ai", "anthropic"]
published: true
---

# はじめに

Claude Codeには、AIの振る舞いをカスタマイズするための4つの主要な仕組み(Hooksは除く)があります。

| 機能          | 配置場所            | 概要                       |
| ------------- | ------------------- | -------------------------- |
| **Rules**     | `.claude/rules/`    | プロジェクト全体のルール   |
| **Commands**  | `.claude/commands/` | 手動で実行するワークフロー |
| **Skills**    | `.claude/skills/`   | 自動/手動両対応の専門知識  |
| **Subagents** | `.claude/agents/`   | 独立したタスク実行         |

本記事では、2025年10月に登場した**Skills**について2026年1月現在のアップデートを含めて状況をまとめてみます。

---

## 1. Skillsの登場

2025年10月16日、AnthropicはClaude Skillsを[リリース](https://claude.com/blog/skills)しました。

Skillsの持っていた主な特徴は以下の通りでした。

- 自動発見（Auto-discovery）: フロントマターの`description`に基づいて、Claudeが関連時に自動でロード
- ポータビリティ: Claude.ai / Claude Code / APIの3プラットフォームで共通フォーマット（"Create once, use everywhere"）
- Progressive Disclosure: メタデータ（約100トークン）のみ常駐し、必要時に詳細をロード

### 当時の課題感

当時の課題感は[当時の記事](https://claude.com/blog/equipping-agents-for-the-real-world-with-agent-skills)によると、以下の通りでした。

> As model capabilities improve, we can now build general-purpose agents that interact with full-fledged computing environments, but as these agents become more powerful, we need more composable, scalable, and portable ways to equip them with domain-specific expertise.

エージェントが複数ドメインに渡る複雑なタスクを実行できるようになってきたことにより、composable（組み合わせ可能）で、scalable（拡張可能）で、portable（ポータブル）な方法でドメイン固有の専門知識を与える必要が出てきました。

> Instead of building fragmented, custom-designed agents for each use case, anyone can now specialize their agents with composable capabilities by capturing and sharing their procedural knowledge.

ユースケースごとに断片化されたカスタム設計のエージェントを構築する代わりに、誰でも手続き的な知識を記録・共有することで、組み合わせ可能な能力を用いてエージェントを専門化できるようになります

この辺りを見るに、専門的なスキルをエージェントに与えることがskillsが生み出された背景だったように見えます。

また、当時の記事を見るとMCPツールのコンテキスト圧迫を課題に感じ、このSkillsの持つProgressive disclosureという特性を評価する声が多かったように感じます。

https://azukiazusa.dev/blog/mcp-tool-context-overflow/#progressive-disclosure%E6%AE%B5%E9%9A%8E%E7%9A%84%E9%96%8B%E7%A4%BA

---

## 2. 登場時のCommands, Rules, Subagents, Skillsの棲み分け

2025年10月のSkills登場時、skillsにはさほど多くの機能が追加されていなかったため4つの機能には明確な役割分担がありました。

[Claude Code公式ドキュメント](https://code.claude.com/docs/en/skills)、[Builder.io記事](https://www.builder.io/blog/agent-skills-rules-commands)

### 登場時の比較表

| 機能      | 呼び出し                          | コンテキスト | 主な用途                       |
| --------- | --------------------------------- | ------------ | ------------------------------ |
| Rules     | セッション開始時に常時適用        | メイン       | 不変のルールを読み込み         |
| Commands  | 手動呼び出し時のみ                | メイン       | 再利用可能なワークフローを提供 |
| Skills    | descriptionに基づいて自動呼び出し | メイン       | 専門知識を提供                 |
| Subagents | descriptionに基づいて自動呼び出し | 独立         | 特定種類のタスクに特化         |

[Claude Code公式ドキュメント](https://code.claude.com/docs/en/skills)
[Builder.io記事](https://www.builder.io/blog/agent-skills-rules-commands)

---

## 3. アップデート履歴と機能の関係変化

次にskills登場後からskillsに関連する主要な（skillsの立場が変化するような）アップデートを見てみます
細かいUXの改善系のアップデートは多数含まれているのですが、ここでは省略しています

[Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)

### v2.0.20: Skills登場

リリース

### v2.0.43: subagentsのskillsフロントマターの追加

subagentsのフロントマターで`skills`フィールドを指定できるようになりました
これによりスタートアップ時にsubagentsに特定のskillsを読み込むことができるようになりました

### v2.1.0: context: fork、agentフィールド

このアップデートでskillsに多数の機能が追加されました

#### skillsにcontext: forkフロントマターの追加

skillsのフロントマターで `context: fork` を指定することで、Skillsをサブエージェントとして実行可能になりました

#### skillsにagentフロントマターの追加

skillsのフロントマターで `agent` フィールドを指定できるようになりました
これにより特定のサブエージェントを指定してskillsを実行可能になりました

### v2.1.3: CommandsとSkillsが統合

CommandsとSkillsが統合されました
これにより`.claude/skills`配下のskillsは`/`で呼び出せるようになりました

---

## 4. 今のSkillsの立ち位置 - 他機能(rules, commands, subagents)との関係性

### Rules と Skills

Rulesとの関係性は発表当初から変わっていないように思えます。
相変わらず初期から読み込まれるrulesと必要なときに呼び出されるskillsという関係性は維持されています。
強いて言えば、subagentsに`skills`フロントマターが追加されたことでsubagentsに関してはrulesの役割と一部重複していると言えるかもしれません。

### Commands と Skills

v2.1.3の統合により、commandsはほとんどskillsの簡易版のようなものになりました。
ユーザーからの呼び出しに関する設定(`user-invocable`)とエージェントからの呼び出しに関する設定(`disable-model-invocation`)もフロントマターに存在する今、個人的にはcommandsをあえて利用する理由も見つかりません。
実際、私はskillsにSKILL.mdのみを配置するスタイルに移行しました。

### Subagents と Skills

これに関しては以下記事で詳しく考察されています

[giginet: SubagentとContext: forkの違い](https://giginet.hateblo.jp/entry/2026/01/27/202636)

結論だけ引用させていただくと

- skillsには情報参照用のReference Contentとタスク実行用のTask Contentがある
- Reference Contentとしてのskillsを利用する際はsubagentsを主とし、`skills`フロントマターに展開する
- Task Contentとしてのskillsを利用する際はskillsを主とし、`agent`フロントマターでskills(Task Content)を実行するエージェントを選択する

---

## まとめ

ここまでskillsの登場から現在までのアップデートの流れを追い、現在の立ち位置を確認しました
わかっていたことではありますが、skillsはclaude codeを拡張する上ではほぼ必須の仕組みになっていると思いつつ、まだrulesとsubagentsとは責務が分かれている点が面白いですね
今後もclaude codeのアップデートを注視していきたいと思います

---

## 参考資料

### 公式

- [Anthropic: Introducing Skills](https://claude.com/blog/skills)
- [Anthropic Engineering: Equipping agents for the real world](https://claude.com/blog/equipping-agents-for-the-real-world-with-agent-skills)
- [Claude Code公式ドキュメント: Skills](https://code.claude.com/docs/en/skills)
- [Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)

### 参考記事

- [Simon Willison: Claude Skills are awesome](https://simonwillison.net/2025/Oct/16/claude-skills/)
- [Builder.io: Agent Skills vs. Rules vs. Commands](https://www.builder.io/blog/agent-skills-rules-commands)
- [Vivek Haldar: Subagents, Commands and Skills Are Converging](https://www.vivekhaldar.com/articles/claude-code-subagents-commands-skills-converging/)
- [giginet: SubagentとContext: forkの違い](https://giginet.hateblo.jp/entry/2026/01/27/202636)
