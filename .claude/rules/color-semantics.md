---
paths:
  - '**/*.kt'
  - '**/*.kts'
---
# 色の意味（手引き 視点A ／ WCAG 1.4.1）

色は手がかりとして有効だが、色だけに頼ると伝わらない人がいる。

## must

- **色だけで情報を伝えない**。状態・カテゴリは **色 ＋ 文字 ＋ アイコン**の三重で示す。
- エラー/成功などの状態は、色に加えて文言・アイコン・semantics（`error` 等）でも表す。

## nice

- 色の慣習（赤=危険・緑=完了・青=情報）は補助として使う。ただし検証済みの手引き所見は「青=男性トイレ／赤=女性トイレ」の性別色分けが伝わりやすい点に限る。

## Compose

```kotlin
// 状態は「色 + 文字 + アイコン + semantics」で冗長化する（色だけに頼らない）
if (isError) Text(
    text = msg,
    color = MaterialTheme.colorScheme.error,          // 色
    modifier = Modifier.semantics { error(msg) },     // semantics（読み上げ）
)                                                     // ＋本文に「エラー」語・アイコンも併記

// オン/オフ状態は stateDescription で読み上げる（色トグルを見分けられない人にも届く）
Modifier
    .toggleable(value = on, role = Role.Switch, onValueChange = onChange)
    .semantics { stateDescription = if (on) "オン" else "オフ" }
```

## 検証

- グレースケール表示でも状態の区別がつくか確認。Accessibility Scanner の色関連チェック。
- テストルールに `enableAccessibilityChecks()` を足すと、色コントラストも操作のたびに自動 fail（横断チェック）。
