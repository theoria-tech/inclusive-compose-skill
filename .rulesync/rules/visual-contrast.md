---
targets: ["*"]
description: "色コントラスト。文字・重要UIと背景の明度差を確保する。Compose で色/テーマ/前景色を扱うときに適用。"
globs: ["**/*.kt", "**/*.kts"]
---

# 視覚：コントラスト（手引き 視点A）

淡い配色は「おしゃれ」だが、認識できなければ使えない。認識させたいものには明度差を付ける。

## must

- テキスト／重要 UI と背景のコントラスト比 **4.5:1 以上**（大きな文字・太字は 3:1 以上）。
- 色は **`MaterialTheme` の `ColorScheme` に集約**し、画面ごと・コンポーネントごとにハードコードしない。
- 主役の配色は **4.5:1 を検証して固定**する（テストで比を assert）。M3 標準ロールペアは設計上コントラストが組み込まれ（最低 3:1・Dynamic Color でも維持）、本文の 4.5:1 の保証まではない。ロールを外れる（カスタム色・非ペア）と保証そのものが消える——静的スキームでも同じ。

## nice

- Material Theme Builder の contrast level（medium / high）出力を利用する。
- OS のコントラスト設定・ダークモードに追従する。
- フォーカス／選択状態のインジケータもコントラストを確保する（枠線だけに頼らない）。

## Compose

```kotlin
val AppColorScheme = lightColorScheme(
  primary = Color(0xFF1848A1), onPrimary = Color(0xFFFFFFFF), // ≈ 8.5:1
)
MaterialTheme(colorScheme = AppColorScheme) { App() } // アプリルートに置く＝画面間で一貫
```

## 検証

- `ColorScheme` の値からコントラスト比を算出する単体テストを書き、4.5:1 を assert。
- Accessibility Scanner / Accessibility Test Framework の `TextContrastCheck`。
