---
targets: ["*"]
description: "サインと目印。重要な導線・見出しを目立たせ、スクリーンリーダーからも辿れるようにする。見出し/セクション/主要導線を扱うときに適用。"
globs: ["**/*.kt", "**/*.kts"]
---

# サイン・目印（手引き 視点B）

どこに何があるか、迷わず見つけられる目印を置く。目でも読み上げでも辿れるように。

## must

- 主要な導線・アクションは、大きさ・位置・ラベルで明確に目立たせる。
- 見出しには **`Modifier.semantics { heading() }`** を付け、TalkBack の見出しジャンプを可能にする。
- 重要な導線をアイコンだけで表現しない（`pictogram-icon` 参照）。

## nice

- 読み上げ順が不自然なら `traversalIndex` / `isTraversalGroup` で整える。

## Compose

```kotlin
// 見出し＝TalkBack の見出しジャンプ対象
Text("当事者様のお名前", Modifier.semantics { heading() })
// 複数要素を1つの意味に合成して読み上げる（例：進捗バー → 「進捗、全10問中2問目」）
Row(Modifier.semantics(mergeDescendants = true) {
    contentDescription = "進捗、全${total}問中 ${current}問目"
}) { /* 個々のセグメントは装飾＝読み上げない */ }
// タップの意味を読み上げに乗せる（「登録」だけでなく「お名前を登録する」と伝わる）
Modifier.clickable(onClickLabel = "お名前を登録する", onClick = onEdit)
```

## 検証

- TalkBack の見出しジャンプで主要セクションに移動できることを確認。
