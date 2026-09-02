---
description: ピクトグラム・アイコン。アイコン単独で伝わる思い込みを禁止し、文字を併記する。アイコン/画像/装飾を扱うときに適用。
applyTo: '**/*.kt,**/*.kts'
---
# ピクトグラム・アイコン（手引き 視点B）

「このアイコンなら誰でも分かる」は作り手の思い込み。文字併記が必須。

## must

- **デザインに無いアイコンを新規に足さない**（忠実優先。アイコンは元のデザインにある場合のみ扱う。「親切心」で絵文字やアイコンを増やさない）。
- 意味を持つアイコンには **文字ラベルを併記**する（アイコンは補助）。
- **装飾アイコンは `contentDescription = null`**。意味はラベルが持つ（二重読み上げ防止）。
- 意味を持つアイコンで文字が無い場合のみ、`contentDescription` に意味を書く。

## nice

- アイコン＋ラベルは 1 コントロールとしてマージして読ませる。
- 複合装飾を完全に読み上げから外すときは `Modifier.clearAndSetSemantics {}`（子孫の semantics を消し、1つの意味に置き換える）。

## Compose

```kotlin
// 装飾アイコンは読み上げない
Icon(Icons.Filled.Call, contentDescription = null)
// アイコン＋ラベルは1コントロールにまとめて読ませる（二重読み上げ防止）
Row(Modifier.semantics(mergeDescendants = true) {}) { Icon(icon, null); Text("ラベル") }
// ナビの絵アイコンは常時ラベル表示（アイコン単独に頼らない）
NavigationBarItem(
    selected, onClick,
    icon = { Icon(tab.icon, null) }, label = { Text(tab.label) },
    alwaysShowLabel = true,
)
// 意味を持つアイコンで文字が無い場合のみ、contentDescription に意味を書く
Icon(Icons.Filled.Warning, contentDescription = "警告")
```

## 背景

福岡市手引きの調査では、トイレの男女マークの理解度が一般 92 点に対し認知症の人は 35 点。アイコン単独の伝達を過信しない。

## 検証

- 装飾アイコンが二重読みされないこと、意味アイコンがラベルで伝わることを TalkBack で確認。
