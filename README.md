# Inclusive Compose UI Skill

AI コーディングツール（Claude Code / Cursor / GitHub Copilot）に **「誰にとってもやさしい Jetpack Compose UI」** を書かせるための設計ルール集。

福岡市「認知症の人にもやさしいデザインの手引き」——ユニバーサルデザインを土台にした合意済みガイド——を、Compose 実装のルールへ翻訳したもの。

## なぜ

AI は「動く UI」は書けるが、コントラスト・読み上げ順・フォントスケール追従・タッチターゲット・戻る安心といった **"見えない層"** を落としがち。その見えない層をルールとして与え、AI が最初からやさしく書けるようにする。

## 2層生成（単一ソース → rules層 ＋ skill層）

真実は **`.rulesync/` の1か所**。ここから各ツール向けに **rules層と skill層の2層**を生成する。手でコピー保守しない。

- **rules層**：ファイルに紐づく詳細ルール（`**/*.kt` / `**/*.kts` で発火）。Claude Code `.claude/rules/`、Cursor `.cursor/rules/`、Copilot `.github/instructions/`。
- **skill層**：全体方針の入口となる `SKILL.md`（**1本のソースから3ツールへ**）。Claude Code `.claude/skills/`、Cursor `.cursor/skills/`、Copilot `.github/skills/`。description で発火し、Claude Code では `/inclusive-compose` で手動起動もできる。

```
.rulesync/rules/          # ★単一ソース①（形式非依存 = 全体方針 overview + 14カテゴリ）
  overview.md             #   root ルール（全体方針）
  navigation / signage / visual-contrast / visual-theme /
  information-density / personalization / copy-language /
  touch-accessibility / error-handling / notification-sound /
  support / pictogram-icon / color-semantics / adaptive-layout
.rulesync/skills/         # ★単一ソース②（skill 入口）
  inclusive-compose/SKILL.md
rulesync.jsonc            # 生成設定（targets / features = rules,skills）
verification.md           # 手動＋自動(Compose UIテスト)＋CI

# 生成物（コミット対象）
# rules層
CLAUDE.md, .claude/rules/*.md              # Claude Code
.cursor/rules/*.mdc                        # Cursor（globs で発火）
.github/copilot-instructions.md,
.github/instructions/*.instructions.md     # Copilot（applyTo で発火）
# skill層（SKILL.md）
.claude/skills/inclusive-compose/SKILL.md  # Claude Code（paths で auto-activate ＋ 手動 /inclusive-compose）
.cursor/skills/inclusive-compose/SKILL.md  # Cursor
.github/skills/inclusive-compose/SKILL.md  # Copilot
```

## 使い方

```bash
# rules層＋skill層を生成（targets / features は rulesync.jsonc から読む）
npx rulesync@16 generate

# ドリフト検知（CI）：ソース編集後に再生成し忘れると落ちる（rules層・skill層とも）
npx rulesync@16 generate --check
```

ルールを直すときは **`.rulesync/rules/*.md`（単一ソース）だけ**を編集し、再生成する。1か所の修正が3形式すべてに伝播する。

## カテゴリと出典

14カテゴリは福岡市手引きの視点（色・サイン・明るさ・親しみ 等）と2原則（記憶に頼らせない／安心して選べる・戻れる）に対応する。各ルールは `[must]`（必須）と `[nice]`（推奨）を持ち、Compose 固有 API と検証観点を含む。

- 出典：福岡市「認知症の人にもやさしいデザインの手引き」
- 土台：ユニバーサルデザイン ／ WCAG（色・コントラスト）

## ライセンス

MIT License（本パックの構成・翻訳部分）。手引きの原典は福岡市に帰属。
