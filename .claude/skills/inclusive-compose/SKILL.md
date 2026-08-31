---
name: inclusive-compose
description: >-
  Inclusive Compose UI ルール集。福岡市『認知症の人にもやさしいデザインの手引き』(ユニバーサルデザインを土台にした具体化) を
  Jetpack Compose 実装へ翻訳した設計ルール。Compose の UI を書く・直すときは必ず参照する。
user-invocable: true
paths:
  - '**/*.kt'
  - '**/*.kts'
---
# Inclusive Compose UI ルール

Jetpack Compose で UI を書くとき、"とりあえず動く UI" ではなく **"誰にとってもやさしい UI"** を書くためのルール集。

- **出典**：福岡市「認知症の人にもやさしいデザインの手引き」。土台はユニバーサルデザイン（UD）で、その合意済みの具体化。
- **狙い**：認知症・MCI の当事者、高齢者、そして一時的に余裕のない全ユーザーに効く UI を、AI が最初から書けるようにする。

## 基本原則（手引きの2原則）

1. **記憶に頼らず行動できる**（迷わせない・覚えさせない）
2. **安心して自分で選べる・戻れる**

## 使い方

- Compose UI を新規に書くとき／既存を直すときは、カテゴリ別ルール（下記「カテゴリ別ルール」）を適用する。
- 各ルールには `[must]`（必須）と `[nice]`（推奨）がある。**must を満たさないコードは出さない**。
- トレードオフがある場合は accessibility 側に倒す。
- 実装後は必ず検証する（`verification.md`）。**「満たしているか？」を AI に判定させない**——実行できるテスト/チェックリストを成果物として出す。

## デザインへの忠実と、ルールとの衝突（優先順位）

**順位：inclusive の [must]（下限）＞ Figma への忠実（意図の正）＞ 余計な装飾を足さない。**

- Figma は**レイアウト・内容・意図**の正。ルールの適用は既にある要素に対して行い、**デザインに無い装飾（アイコン・ラベル・余分な要素）を勝手に足さない**。「目立たせる」はまず既存要素の色・大きさ・semantics で満たす。
- ただし **inclusive の [must] は"下限（floor）"** で、Figma より優先する。**デザイン自体が [must] を割る場合**（コントラスト<4.5:1・タップ<48dp・色のみで区別・文字<16sp 等）は——
  1. **黙って再現しない**（下限を割った UI を出さない）／**黙って変えない**（デザイン意図の無断改変を避ける）。
  2. **警告を出す**：該当箇所に `// ⚠️ WCAG 1.4.3: chip の薄いグレー文字 on white ≈ 3.3:1 < 4.5:1（デザイン課題）` のように明示し、まとめて「デザイン課題リスト」として surface する。
  3. **最小限の inclusive 修正**を当て、**理由を記録**（デザイナーが Figma 側に反映できるように）。

## よくある誤適用（やってはいけない）

- **デザインに無いアイコンや要素を足して"親切"にする**（忠実さを壊す。原則は既存要素へ適用する）。
- 装飾アイコンに `contentDescription` を付けて二重に読み上げさせる。
- 本文テキストに `autoSize` を撒いてユーザーのフォントスケール設定を打ち消す。
- `liveRegion` を乱用して読み上げを騒がしくする。
- コントラスト比を計算せずに「4.5:1 を満たした」と主張する。

## カテゴリ別ルール（詳細は rules 層を参照）

このスキルは入口（全体方針）で、詳細は同じ単一ソースから生成される rules 層の 14 カテゴリにある。Claude Code では `.claude/rules/<name>.md`、Cursor では `.cursor/rules/<name>.mdc`、Copilot では `.github/instructions/<name>.instructions.md` に同時生成される（いずれも `**/*.kt` / `**/*.kts` で発火）。

1. `navigation` — ナビゲーションと戻る安心（現在地・次の一歩の明示、自動下書き、Predictive Back）
2. `signage` — サイン・見出し（どこで何ができるかを明示）
3. `visual-contrast` — コントラスト（WCAG 4.5:1 を下限に）
4. `visual-theme` — 明るさ・テーマ（ダーク／ハイコントラスト追従）
5. `information-density` — 情報密度（一度に見せすぎない）
6. `personalization` — 個人化（フォントスケール・設定の尊重）
7. `copy-language` — 文言（やさしい日本語、命令ではなく案内）
8. `touch-accessibility` — タッチ・アクセシビリティ（48dp・semantics）
9. `error-handling` — エラー（error セマンティクス、責めない文言、回復導線）
10. `notification-sound` — 通知・音（色/音だけに頼らない）
11. `support` — サポート・助けの導線
12. `pictogram-icon` — ピクトグラム・アイコン（意味の伝達、装飾は読み上げさせない）
13. `color-semantics` — 色の意味付け（色のみで区別しない）
14. `adaptive-layout` — アダプティブレイアウト（Android 17 大画面必須化に対応）

## 検証（必須）

Skill は「書いて終わり」ではない。判定を主張させず、実行できる成果物で検証する。詳細は `verification.md`。

- **手動**：TalkBack（読み上げ順・内容）、フォントスケール 200%、ハイコントラスト／ダーク、Accessibility Scanner。
- **自動（Compose UI テスト）**：`assertIsSelected` / `assertHeightIsAtLeast(48.dp)`（48dp は高さの下限＝heightIn を突く） / error セマンティクス、`enableAccessibilityChecks()`、コントラスト比を値からアサート。
- **CI**：`npx rulesync generate --check`（単一ソースの再生成し忘れ検知）＋ `./gradlew connectedCheck`。

検証で見つかった粗は **`.rulesync/rules/*.md`（単一ソース）に反映 → 再生成**。1 か所直せば全形式に伝播する。
