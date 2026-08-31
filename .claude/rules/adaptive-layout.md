---
paths:
  - '**/*.kt'
  - '**/*.kts'
---
# アダプティブ・レイアウト（UD × WCAG 1.4.10 Reflow）

どの画面サイズ・姿勢（大画面・折りたたみ・分割画面・片手ウィンドウ・外部ディスプレイ・拡大表示）でも、同じ情報と操作に迷わず辿り着けるようにする。

- 手引き本文に直接の記述はないが、原則①「迷わせない」を**画面サイズの多様性**へ広げたもの。土台は UD、根拠は WCAG 2.1 **1.4.10 Reflow**（拡大しても情報・機能を失わせず、2次元スクロールをさせない）。
- Android 17（targetSdk 37）は sw600dp 以上で向き固定・リサイズ不可を OS が無視する（Android 16/targetSdk 36 で導入・一時オプトアウト → 37 で廃止）。**アダプティブ対応は"やった方がよい"から"前提"へ**。

## must

- 幅の分岐は `WindowSizeClass`（Compact <600dp / Medium 600–840dp / Expanded ≥840dp）で行い、Compact＝1列 → Expanded＝複数ペインに **reflow** する。散らばった固定 dp のマジックナンバー分岐にしない。
- 分岐の計算は **`WindowSizeClass.calculateFromSize(DpSize)`**（純関数）を使う。`calculateWindowSizeClass(activity)` は Activity 依存で `@Preview`／スクショテストで計算できないので使わない。
- 向き固定（`android:screenOrientation`）や `android:resizeableActivity="false"` を書かない（targetSdk 37 では sw600dp+ で無視。Android 16 の opt-out `PROPERTY_COMPAT_ALLOW_RESTRICTED_RESIZABILITY` は一時措置で 37 廃止）。
- 広い画面を**引き伸ばしで埋めない**。1列を横に伸ばして1行を長くしたり、固定幅中央寄せで両脇を余白で捨てるのでなく、情報の配置を組み替える（1.4.10：行長を読める範囲に保つ）。
- レイアウトが切り替わっても**表示内容・操作・読み上げ/フォーカス順を同一に保つ**（列→行に組み替えても意味の順序を変えない。サイズで機能を増減させない）。

## nice

- ナビは `NavigationSuiteScaffold`（compact＝バー / expanded＝レール）、一覧+詳細は **Navigation 3 なら `ListDetailSceneStrategy`（`rememberListDetailSceneStrategy` を `NavDisplay` の `sceneStrategies` に渡す・`androidx.compose.material3.adaptive:adaptive-navigation3`）** に寄せる（公式アダプティブ skill は Nav3 文脈で `ListDetailPaneScaffold`/`SupportingPaneScaffold` を「使うな」＝非推奨。ただし両者は **非 deprecated**〔stable 1.3.0〕で、Nav3 非採用なら従来どおり可）。※既定で `currentWindowAdaptiveInfo()`（Activity ウィンドウ依存）を使うため、`@Preview` では directive（`layoutType`／`scaffoldDirective` 相当）を明示的に渡す。
- 回転・折りたたみ・リサイズの再生成で入力状態を失わせない（`rememberSaveable`／ViewModel。navigation ルールの「戻っても失わせない」と同じ）。

## Compose

```kotlin
// 画面ルートで測る。calculateFromSize は純関数なので @Preview/スクショでも同じ結果になる。
// BoxWithConstraints は「親の制約」を測るので画面ルートに置く（scroll 内は maxHeight=∞ になるため幅で判断）。
@OptIn(ExperimentalMaterial3WindowSizeClassApi::class)
@Composable
fun ReportRoute(modifier: Modifier = Modifier) = BoxWithConstraints(modifier.fillMaxSize()) {
    val expanded = WindowSizeClass
        .calculateFromSize(DpSize(maxWidth, maxHeight))
        .widthSizeClass == WindowWidthSizeClass.Expanded
    if (expanded) {
        Row { ReportBody(Modifier.weight(1f)); ReportActions(Modifier.weight(1f)) } // 2ペインに reflow
    } else {
        Column { ReportBody(); ReportActions() } // 1列。順序は Row 時と同じ
    }
}
// NG: calculateWindowSizeClass(activity) — Activity 依存で @Preview では計算できない
```

## 検証

- `@Preview(widthDp = 360)` と `@Preview(widthDp = 900)` の2枚をスクショテストに含め、1列→複数ペインに reflow することを画像で確認。
- `fontScale = 1.5f` を掛けた expanded プレビューで、はみ出し・見切れ・重なりが無いことを確認。
- Manifest を grep し、`screenOrientation` 固定と `resizeableActivity="false"` が無いことを確認。
- 実機/エミュ：分割画面・回転・ウィンドウリサイズ後に入力途中の状態が残ることを確認。
