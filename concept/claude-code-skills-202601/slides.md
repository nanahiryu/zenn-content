---
marp: true
theme: default
paginate: true
size: 16:9
header: "Claude Code Skills ガイド"
style: |
  section {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: #ffffff;
    font-family: 'Segoe UI', 'Helvetica Neue', sans-serif;
  }
  section.lead {
    text-align: center;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  }
  section.lead h1 {
    font-size: 3.0em;
    font-weight: 700;
    text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
    margin-bottom: 0.5em;
  }
  h1 {
    color: #fff;
    font-size: 2.0em;
    border-bottom: 3px solid rgba(255,255,255,0.3);
    padding-bottom: 0.3em;
    margin-bottom: 0.8em;
  }
  h2 {
    color: #ffd700;
    font-size: 1.8em;
    margin-top: 1em;
    margin-bottom: 0.5em;
  }
  h3 {
    color: #ffeb3b;
    font-size: 1.4em;
    margin-bottom: 0.4em;
  }
  a {
    color: #64ffda;
    text-decoration: none;
  }
  a:hover {
    text-decoration: underline;
  }
  code {
    background: rgba(0,0,0,0.3);
    padding: 0.2em 0.4em;
    border-radius: 4px;
    color: #64ffda;
  }
  pre {
    background: rgb(245, 245, 250);
    border-radius: 8px;
    padding: 1em;
    box-shadow: 0 4px 6px rgba(0,0,0,0.3);
    border: 1px solid rgba(0,0,0,0.1);
  }
  pre code {
    background: transparent;
    color: rgb(40, 40, 50);
  }
  table {
    border-collapse: collapse;
    width: 100%;
    margin: 1em 0;
    background: rgb(102, 126, 234);
    border-radius: 8px;
    overflow: hidden;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
  }
  th {
    background: rgb(118, 75, 162);
    color: #ffeb3b;
    font-weight: 600;
    padding: 0.8em;
    text-align: left;
    border-bottom: 2px solid rgba(255, 235, 59, 0.5);
  }
  td {
    padding: 0.8em;
    border-bottom: 1px solid rgba(255,255,255,0.2);
    background: rgb(90, 110, 220);
    color: #ffffff;
  }
  tr:last-child td {
    border-bottom: none;
  }
  td strong {
    color: #ffeb3b;
  }
  strong {
    color: #ffd700;
    font-weight: 600;
  }
  blockquote {
    border-left: 4px solid #ffd700;
    background: rgba(0,0,0,0.2);
    padding: 1em;
    margin: 1em 0;
    border-radius: 4px;
  }
  ul, ol {
    margin-left: 1.5em;
  }
  li {
    margin-bottom: 0.5em;
    line-height: 1.6;
  }
  header {
    color: rgba(255,255,255,0.6);
    font-size: 0.9em;
  }
  footer {
    color: rgba(255,255,255,0.6);
    font-size: 0.9em;
  }
  section::after {
    color: rgba(255,255,255,0.6);
  }
---

<!-- _class: lead -->
<!-- _paginate: false -->
<!-- _header: '' -->

# Claude Code Skills の紹介

Commands、Rules、Subagentsとの違いから実践まで

---

## claude code使ってますか？

---

## アップデートの入り方がえぐい

![width:800](images/changelog.png)

---

## Claude Codeをカスタマイズする4つの方法

- **Rules** - プロジェクトのルール
- **Commands** - 手動で実行するワークフロー
- **Skills** - 自動/手動両対応の専門知識
- **Subagents** - 独立したタスク実行

**今日はSkillsにフォーカスします**

---

## アジェンダ

1. Skillsとは
2. 4つの拡張方法の違い
3. Skillsの定義の仕方
4. Task Contents vs Reference Contents
5. まとめ

---

<!-- _class: lead -->

# 1. Skillsとは

---

## Skillsの特徴

- 専門的な知識を提供するための仕組み
- スクリプトとかテンプレートとか関連性のあるものをまとめて提供できる

---

<!-- _class: lead -->

# 2. 4つの拡張方法の違い

---

## 4つの拡張方法

| 機能          | 配置場所            | いつ使う？                   |
| ------------- | ------------------- | ---------------------------- |
| **Rules**     | `.claude/rules/`    | プロジェクトの基本方針を定義 |
| **Commands**  | `.claude/commands/` | 簡単なワークフローを定義     |
| **Skills**    | `.claude/skills/`   | 色々できすぎるので割愛       |
| **Subagents** | `.claude/agents/`   | 独立したタスクを実行する     |

---

## コンテキストの復習

![width:720](images/context.png)

- コンテキストと呼ばれる記憶領域をセッションごとに持っている
- コンテキストが満たされるとauto compactが発生し、それまでの記憶が圧縮され、出力精度が悪くなるため、コンテキストの節約が重要

---

## 他の拡張方法との違い

| 機能          | 違い                                                                                                               |
| ------------- | ------------------------------------------------------------------------------------------------------------------ |
| **Rules**     | rulesは常に読み込まれる、skillsはフロントマターのname, descriptionに基づいて読み込まれる(か、明示的に呼び出す)     |
| **Commands**  | 今はskillsの簡易版のような立ち位置（かつては明示的呼び出しがcommands, 暗黙的な呼び出しがskillsだったが統合された） |
| **Subagents** | subagentsは独立したコンテキストで実行される、skillsは同一のコンテキストで実行される                                |

---

## まとめると

- 必要なときに読み込める
- フロントマターのname, descriptionに基づいてclaudeが判断して読み込む
- `/`で強制的に呼び出すこともできる
- 基本的には呼び出したエージェントで展開、動作する（例外あり）

---

<!-- _class: lead -->

# 3. Skillsの定義の仕方

---

## 基本構造

```
.claude/skills/my-skill/
├── SKILL.md          # メインの指示（必須）
├── reference.md      # 詳細なリファレンス（オプション）
├── template.md      # テンプレートファイル（オプション）
└── scripts/
    └── helper.py     # 実行可能スクリプト（オプション）
```

**SKILL.md** が必須、他はオプション
サポートファイルで機能を拡張できる

---

## SKILL.mdの構造

```yaml
# フロントマター
---
name: my-skill
description: Skillの説明（Claudeがいつ使うか判断する）
---
# Skillの指示内容をMarkdownで記述
```

---

## フロントマターの主要フィールド

| フィールド          | 説明                                     |
| ------------------- | ---------------------------------------- |
| `name`(必須)        | Skillの名前（`/name` として呼び出せる）  |
| `description`(必須) | Claudeが自動発見に使う説明（重要）       |
| `context: fork`     | 独立コンテキストで実行                   |
| `agent`             | subagentとして実行するエージェントの指定 |
| `allowed-tools`     | 許可するツールのリスト                   |

詳しくは
https://code.claude.com/docs/ja/skills#%E3%83%95%E3%83%AD%E3%83%B3%E3%83%88%E3%83%9E%E3%82%BF%E3%83%BC%E3%83%AA%E3%83%95%E3%82%A1%E3%83%AC%E3%83%B3%E3%82%B9

---

<!-- _class: lead -->

# 4. Task Contents vs Reference Contents

---

## Skillsの2つのタイプ

Skillsは用途に応じて**2つのタイプ**に分けられる

### Reference Content

**知識を提供するためのskills**

例: コーディングルール、obcのAPI仕様

### Task Content

**複雑なワークフロー(途中でscripts実行させたり、成果物にtemplateがあるもの等)**

例: 実行のプランニング、プランに基づいた実装、仕様書の作成

---

## Reference Content

```yaml
---
name: kintone-api-guidelines
description: |
  WHEN: kintone API を実装する時、kintone REST API を呼び出す時、kintone イベントハンドラを書く時
  WHEN NOT: UI のみの実装時、ドキュメント作成時、テスト実行時
user-invocable: false
---
# kintone plugin 開発上のルール

## 原則

kintone オブジェクト（`kintone.events`, `kintone.api`, `kintone.app` など）を使用する際は、

**必ず実装前に公式ドキュメントを WebFetch, WebSearch で参照してください**。
...
```

---

## Task Content

```yaml
---
name: design-test
description: 仕様書に基づいてテスト仕様書を作成する。WHEN: /design-test 実行時
context: fork
agent: coder
---

# /design-test - テスト設計

## 手順

### Step 1: 仕様書の確認

`docs/spec/` 配下の仕様書を確認:

- @docs/spec/concept.md - プラグイン概要
...
```

---

## 使い分けのポイント

| 観点                 | Reference Content    | Task Content           |
| -------------------- | -------------------- | ---------------------- |
| **目的**             | 知識提供             | アクション実行         |
| **呼び出し**         | 自動（Claudeが判断） | 手動（ユーザーが起動） |
| **`user-invocable`** | `false`              | `true` (デフォルト)    |
| **`context: fork`**  | `false` (デフォルト) | `true` (推奨)          |

---

<!-- _class: lead -->

# 5. まとめ

---

## まとめ

### Skillsの利用方法

専門的な知識や複雑なフローを実行する際に利用する

### commandsと統合された

Commands、Subagentsとの統合で統一的な仕組みに

### subagentsとも事実的に統合されつつある

フロントマターでsubagentsにskillを実行させたり、contextのフォークができるように

### Task vs Reference Contentsを意識

用途に応じて使い分けると効果的

---

## 参考資料

### 公式ドキュメント

- [Claude: Skills explained](https://claude.com/blog/skills-explained)
- [Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
- [Extend Claude with skills - Claude Code Docs](https://code.claude.com/docs/en/skills)

### 参考記事

- [Builder.io: Agent Skills vs. Rules vs. Commands](https://www.builder.io/blog/agent-skills-rules-commands)
- [Understanding Claude Code: Skills vs Commands vs Subagents](https://www.youngleaders.tech/p/claude-skills-commands-subagents-plugins)
- [Claude CodeのSubagentとcontext: forkは何が違うのか - 5.1さらうどん](https://giginet.hateblo.jp/entry/2026/01/27/202636)
