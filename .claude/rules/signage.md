---
paths:
  - '**/*.kt'
  - '**/*.kts'
---
# サイン・目印（手引き 視点B）

どこに何があるか、迷わず見つけられる目印を置く。目でも読み上げでも辿れるように。

## must

- 主要な導線・アクションは、大きさ・位置・ラベルで明確に目立たせる。
- 見出しには **`Modifier.semantics { heading() }`** を付け、TalkBack の見出しジャンプを可能にする。
- 重要な導線をアイコンだけで表現しない（`pictogram-icon` 参照）。

## nice

- 読み上げ順が不自然なら `traversalIndex` / `isTraversalGroup` で整える。

## 検証

- TalkBack の見出しジャンプで主要セクションに移動できることを確認。
