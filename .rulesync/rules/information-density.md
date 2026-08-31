---
targets: ["*"]
description: "情報量。1画面1タスクに絞り、認知負荷を下げる。画面/フォーム/リストの情報設計を扱うときに適用。"
globs: ["**/*.kt", "**/*.kts"]
---

# 情報の量（手引き：記憶に頼らせない）

一度に見せる情報が多いと、選べない・決められない。絞ることがやさしさ。

## must

- **1画面 1 タスク**を基本にする。関係ない情報を同時に出さない。
- 選択肢は多すぎない数に絞る。長い選択肢文は折り返して全文を見せる（切らない）。

## nice

- 段階的開示（必要になったら追加情報を出す）。

## Compose

```kotlin
Text(choiceLabel) // 選択肢・本文ラベルは maxLines / Ellipsis で切らない（softWrap 既定で全文が折り返る）
var expanded by rememberSaveable { mutableStateOf(false) } // 段階的開示は開閉状態を保持
if (expanded) Text(detail) // 必要になってから追加情報を出す（AnimatedVisibility でも可）
```

## 検証

- 主要タスク画面で「今やること」が一目で分かるかレビュー。フォントスケール 200% で全文が見切れないか確認。
