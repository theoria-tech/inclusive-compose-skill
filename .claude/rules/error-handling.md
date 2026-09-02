---
paths:
  - '**/*.kt'
  - '**/*.kts'
---
# エラーハンドリング（手引き：安心して選べる／Error）

エラーは「突き放し」でなく「次の一歩の案内」。目で読めない人にも同じ内容が届くようにする。

## must

- 文言は **原因 ＋ 解決策**。`"Invalid input"` のような突き放しは禁止。寄り添うトーンで。
- **表示文言と読み上げ文言を統一**する（別々に書かない）。
- semantics に **`error(...)`** を付与する（新 API ではなく、AI が付け忘れがちな既存 API）。
- 重要なメッセージは **自動で消さない**（Snackbar は `SnackbarDuration.Indefinite` ＋明示的な閉じる導線）。

## nice

- 動的に出る変化は `liveRegion = LiveRegionMode.Polite`。見出しには `heading()`。
- `announceForAccessibility` は Android 16 で非推奨。後継＝ `error` / `liveRegion` / `stateDescription`（画面遷移級は `paneTitle`）。
- 重大なエラーや完了は `liveRegion = LiveRegionMode.Assertive`（今の読み上げに割り込む）、通常は `Polite`。使いすぎない。
- キーボード/スイッチ操作の人向けに、インラインのエラー文へ入力焦点を移す（`navigation` の ## Compose 参照。読み上げは上の `liveRegion` が担う）。

## Compose

```kotlin
val msg = "メールアドレスの形式が違います。@ が含まれているか確認してください"
OutlinedTextField(
  value = email, onValueChange = onChange, isError = isError,
  supportingText = { if (isError) Text(msg, Modifier.semantics { liveRegion = LiveRegionMode.Polite }) },
  modifier = Modifier.semantics { if (isError) error(msg) },
)
```

## 検証

- error semantics が付くことを assert：`onNode(hasText("…部分文言…", substring = true)).assert(SemanticsMatcher.keyIsDefined(SemanticsProperties.Error))`（フル一致なら `onNodeWithText(fullMessage).assert(...)`）。**架空文でなく実文言に一致させる**。
- TalkBack で Before（"Invalid input"）→ After（原因＋解決策）の読み上げを聞き比べ。
