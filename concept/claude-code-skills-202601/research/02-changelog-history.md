# Claude Code Skills - Changelog履歴

## 概要

Claude Code Skills機能のChangelogに記載された変更履歴を時系列でまとめる。

---

## v2.1.0 - 大型アップデート

### 新機能

#### ホットリロード
```
Added automatic skill hot-reload - skills created or modified in
~/.claude/skills or .claude/skills are now immediately available
without restarting the session.
```
- ファイル保存で即座に反映
- セッション再起動不要
- 開発イテレーションの大幅改善

#### context: fork
```
Added support for running skills and slash commands in a forked
sub-agent context using context: fork in skill frontmatter.
```
- フロントマターで `context: fork` を指定
- サブエージェントとしてフォークされたコンテキストで実行

#### agentフィールド
- スキルのフロントマターで `agent` フィールドを指定可能に
- エージェントタイプの明示的な指定

#### フック機能
```
Added hooks support to skill and slash command frontmatter
```
- PreToolUse
- PostToolUse
- Setup
- Stop

#### once: true
```
Added support for once: true setting in frontmatter
```
- 一度だけ実行するスキル設定

#### YAML形式リスト対応
```
Added support for YAML-style lists in frontmatter allowed-tools
field for cleaner skill declarations.
```

#### /contextの修正
- スキルのトークン推定値を正確に反映するように修正

---

## v2.1.3 - スラッシュコマンド統合

### 変更
```
Merged slash commands and skills, simplifying the mental model
with no change in behavior.
```
- スラッシュコマンドとスキルを統合
- メンタルモデルの簡略化
- 動作に変更なし（後方互換性維持）

---

## v2.1.19 - 引数アクセスの改善

### 新機能
```
Added $0, $1, etc. shorthand syntax for accessing arguments
in custom commands
```
- `$0`, `$1` などのショートハンド構文

### 変更
```
Changed argument indexing syntax from $ARGUMENTS.0 to
$ARGUMENTS[0] (bracket syntax)
```
- `$ARGUMENTS.0` → `$ARGUMENTS[0]` に変更（括弧構文）

```
Changed skills with no permissions and no hooks to be allowed
without approval
```
- 権限なし・フックなしのスキルは承認不要に

### 修正
- `/rename`と`/tag`コマンドが異なるディレクトリからのセッション再開時に正しいセッションを更新しない問題を修正

---

## その他の改善

### Skills as separate category
```
Added Skills as a separate category in the context visualization.
```
- コンテキスト可視化でSkillsを独立カテゴリとして表示

### セッションID取得
```
Added ${CLAUDE_SESSION_ID} string substitution for skills to
access the current session ID.
```

### 自動検出の改善
```
Added automatic discovery of skills from nested .claude/skills
directories when working with files in subdirectories.
```
- サブディレクトリの `.claude/skills` も自動検出

### バグ修正
```
Fixed false skill duplicate detection on filesystems with large
inodes (e.g., ExFAT) by using 64-bit precision for inode values.
```
- ExFATなど大きなinode値を持つファイルシステムでの誤検出修正

```
Fixed project-level skills not loading with --setting-sources 'project'.
```
- `--setting-sources 'project'` 時のプロジェクトレベルスキル読み込み修正

---

## 出典

- [Claude Code CHANGELOG.md](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
- [GitHub Releases](https://github.com/anthropics/claude-code/releases)
