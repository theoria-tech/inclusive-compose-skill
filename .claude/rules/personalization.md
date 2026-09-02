---
paths:
  - '**/*.kt'
  - '**/*.kts'
---
# パーソナライズ / 文字サイズ（手引き 視点B コラム「文字の大きさの目安」＋ OS 設定尊重の拡張）

見え方は人によって違う。ユーザーの OS 設定を打ち消さない。

## must

- 文字は最小 **16sp** 相当（`bodyLarge` 等）。**OS フォントスケールに追従**する。
- テキストを **固定高さでクリップしない**。長文は折り返す。
- `autoSize` は **見出し限定**。本文に撒いてユーザーのスケール設定を打ち消さない。

## nice

- 見出しを枠に収めたいときのみ `TextAutoSize.StepBased`（`maxLines` かサイズ制約とセット）。

## Compose

```kotlin
Text(body, style = MaterialTheme.typography.bodyLarge) // スケール追従・固定高さなし
Text(title, maxLines = 1, autoSize = TextAutoSize.StepBased(minFontSize = 20.sp, maxFontSize = 34.sp))
```

## 検証

- `@PreviewTest @Preview(fontScale = 2.0f)` をスクショテストに含め、200% で本文が見切れないこと・見出しが枠に収まることを画像で確認する。
