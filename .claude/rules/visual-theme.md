---
paths:
  - '**/*.kt'
  - '**/*.kts'
---
# 視覚：テーマの一貫性（手引き 視点A/C）

画面ごとに見た目が変わると、同じアプリだと認識しづらい。一貫性は安心につながる。

## must

- **背景色・配色は画面ごとに変えない**。アプリルートの `MaterialTheme` に集約する。
- 同じ意味のコンポーネント（ボタン・カード）は同じ見た目にする。

## nice

- 明るさ（コントラスト/ダークモード）の切り替えに全画面で追従する。

## Compose

```kotlin
MaterialTheme(colorScheme = AppColorScheme, typography = AppTypography) { App() }

// M3 の ColorScheme に無い色（見出し色・チップ・ナビ選択色）は staticCompositionLocalOf で
// テーマに束ねる＝画面ごとにハードコードせず一貫させる
@Immutable data class AppExtraColors(
    val heading: Color = Color(0xFF452910),
    val chipContainer: Color = Color(0xFF7E6E60),
)
val LocalAppExtraColors = staticCompositionLocalOf { AppExtraColors() }
CompositionLocalProvider(LocalAppExtraColors provides AppExtraColors()) {
    MaterialTheme(colorScheme = AppColorScheme) { App() }
}
```

## 検証

- 複数画面のスクリーンショットを並べ、背景・主要色が一致することを確認。
