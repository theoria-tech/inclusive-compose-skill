---
root: true
targets: ["*"]
description: "Inclusive Compose UI ルール集。福岡市『認知症の人にもやさしいデザインの手引き』(ユニバーサルデザインを土台にした具体化) を Jetpack Compose 実装へ翻訳した設計ルール。Compose の UI を書く・直すときは必ず参照する。"
globs: ["**/*.kt", "**/*.kts"]
---

# Inclusive Compose UI ルール

Jetpack Compose で UI を書くとき、"とりあえず動く UI" ではなく **"誰にとってもやさしい UI"** を書くためのルール集。

- **出典**：福岡市「認知症の人にもやさしいデザインの手引き」。土台はユニバーサルデザイン（UD）で、その合意済みの具体化。
- **狙い**：認知症・MCI の当事者、高齢者、そして一時的に余裕のない全ユーザーに効く UI を、AI が最初から書けるようにする。

## 基本原則（手引きの2原則）

1. **記憶に頼らず行動できる**（迷わせない・覚えさせない）
2. **安心して自分で選べる・戻れる**

## 使い方

- Compose UI を新規に書くとき／既存を直すときは、カテゴリ別ルール（`rules/` 配下）を適用する。
- 各ルールには `[must]`（必須）と `[nice]`（推奨）がある。**must を満たさないコードは出さない**。
- トレードオフがある場合は accessibility 側に倒す。
- 実装後は必ず検証する（`verification.md`）。**「満たしているか？」を AI に判定させない**——実行できるテスト/チェックリストを成果物として出す。

## デザインへの忠実と、ルールとの衝突（優先順位）

**順位：inclusive の [must]（下限）＞ Figma・DESIGN.md への忠実（意図の正）＞ 余計な装飾を足さない。**

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

## Compose アクセシビリティ API 早見（どの困りごとにどの API か）

| 困りごと | Compose API | 主に使うルール |
|---|---|---|
| 選択状態を読み上げ | `Modifier.selectable(selected, role = Role.RadioButton)` ＋親 `selectableGroup()` | touch-accessibility |
| オン/オフ状態を読み上げ | `Modifier.toggleable(value, role = Role.Switch, onValueChange)` ＋ `stateDescription` | color-semantics / notification-sound |
| 複合要素を1つの読み上げにまとめる | `Modifier.semantics(mergeDescendants = true){ contentDescription = … }` | signage / pictogram-icon |
| 装飾を読み上げから外す | `contentDescription = null` ／ `Modifier.clearAndSetSemantics {}` | pictogram-icon |
| タップの意味を伝える | `Modifier.clickable(onClickLabel = "…", onClick = …)` | navigation / support |
| 動的メッセージを即読み上げ | `Modifier.semantics{ liveRegion = LiveRegionMode.Polite }` | error-handling / notification-sound |
| エラーを semantics に載せる | `Modifier.semantics{ error(msg) }` | error-handling |
| 見出しジャンプ | `Modifier.semantics{ heading() }` | signage |
| 読み上げ順の調整 | `isTraversalGroup` / `traversalIndex` | signage / touch-accessibility |
| 画面遷移級の変化を告知 | `Modifier.semantics{ paneTitle = "…" }` | navigation / adaptive-layout |
| 行長を保つ reflow | `Modifier.widthIn(max = …)` ／ `WindowSizeClass.calculateFromSize` | adaptive-layout |
| 横断 a11y チェック | テストで `enableAccessibilityChecks()`（`ui-test-junit4-accessibility`） | 各ルールの検証 |
