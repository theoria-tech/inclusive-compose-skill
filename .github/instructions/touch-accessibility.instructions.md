---
description: >-
  タッチターゲットと読み上げ。最小48dp・アイコンに文字併記・selectable+role・装飾は非読み上げ。クリック可能/選択可能な Compose
  要素に適用。
applyTo: '**/*.kt,**/*.kts'
---
# タッチ & 読み上げ（手引き 視点B ＋ Material／アクセシビリティ由来の拡張）

運動機能が落ちた指、部分しか見ていない目のために、押しやすく・伝わる形にする。

## must

- タップ領域は **最小 48dp**：`Modifier.minimumInteractiveComponentSize()`（Material Design タッチターゲット指針由来・手引きには無い拡張）。
- アイコンだけのボタンにしない。**文字ラベルを併記**する。
- 選択肢は `Modifier.selectable(selected, role = Role.RadioButton, onClick = …)`、親に `Modifier.selectableGroup()`。
- **装飾アイコンは `contentDescription = null`**。意味はラベルが持つ（二重読み上げ防止）。

## nice

- アイコン＋ラベルは 1 コントロールとしてマージして読ませる。
- 読み上げ順が不自然なら `traversalIndex` / `isTraversalGroup` で整える。

## Compose

```kotlin
Row(Modifier
    .minimumInteractiveComponentSize()
    .selectable(selected, role = Role.RadioButton, onClick = onToggle)
) { Icon(icon, contentDescription = null); Text("選択肢のラベル") } // 親に Modifier.selectableGroup()
```

## 検証

- `onNodeWithText("選択肢のラベル").assertIsSelected()`
- `onNodeWithText("選択肢のラベル").assertHeightIsAtLeast(48.dp)`（48dp は heightIn の下限＝高さで担保。TouchWidth 等値ではない）
