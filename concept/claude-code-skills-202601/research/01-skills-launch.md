# Skills機能の誕生 - 2025年10月16日

## 概要

2025年10月16日、AnthropicはClaude Skillsを正式発表。Claude.ai、Claude Code、APIの3プラットフォームで同時に利用可能となった。

## Skillsとは

> Skills are conceptually extremely simple: a skill is a Markdown file telling the model how to do something, optionally accompanied by extra documents and pre-written scripts that the model can run to help it accomplish the tasks described by the skill.

- Markdownファイルベースの指示書
- オプションでスクリプトやリソースを含められる
- 必要な時だけ自動的にロードされる

## 初期リリースのスキル

- docx（Word文書作成）
- pdf（PDF操作）
- pptx（PowerPoint作成）
- xlsx（Excel操作）
- algorithmic-art
- canvas-design

## 設計思想: "Create once, use everywhere"

Skillsはポータブルに設計されている：
- 同じフォーマットがどこでも動作
- 一度作れば Claude apps / Claude Code / API で利用可能

## 対象ユーザー

- Pro
- Max
- Team
- Enterprise

## 自動呼び出し

> Claude automatically invokes relevant skills based on your task—no manual selection needed.

ユーザーが明示的に呼び出す必要なく、タスクに応じてClaudeが自動的に関連スキルを選択・ロードする。

## 出典

- [Simon Willison: Claude Skills are awesome, maybe a bigger deal than MCP](https://simonwillison.net/2025/Oct/16/claude-skills/)
- [Anthropic公式ブログ](https://claude.com/blog/skills)
