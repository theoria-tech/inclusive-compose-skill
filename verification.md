# 検証（Inclusive Compose UI）

Skill は「書いて終わり」ではない。AI はルールを誤適用するので、**判定を主張させず、実行できる成果物で検証する**。

## 1. 手動（まず人の目・耳）

- **TalkBack**：読み上げ順・読み上げ内容。装飾の二重読み・エラー文言を確認。
- **フォントスケール 200%**：本文が見切れないか、見出しが枠に収まるか。
- **ハイコントラスト／ダークモード**：配色が破綻しないか。
- **Accessibility Scanner**：ラベル欠落・低コントラスト・48dp 未満を検出。

## 2. 自動（Compose UI テスト）

semantics を機械で固定する。

```kotlin
@Test fun 選択肢は選択状態を読み上げる() {
  rule.onNodeWithText("選択肢のラベル").assertIsSelected()
}
@Test fun タッチターゲットは48dpが確保される() {
  rule.onNodeWithText("選択肢のラベル").assertHeightIsAtLeast(48.dp)
}
@Test fun エラーにerrorセマンティクスが付く() {
  rule.onNode(hasText("お名前が未設定", substring = true))
    .assert(SemanticsMatcher.keyIsDefined(SemanticsProperties.Error))
}
```

横断チェック（Compose 1.8+）：

```kotlin
// 依存: androidx.compose.ui:ui-test-junit4-accessibility
@Before fun enableA11yChecks() { composeTestRule.enableAccessibilityChecks() }
// 以降のアクション実行時に ラベル欠落・低コントラスト・48dp未満 を自動 fail
```

コントラストは値から算出してアサート：

```kotlin
@Test fun primaryとonPrimaryは4_5対1以上() {
  assertThat(contrastRatio(AppColorScheme.primary, AppColorScheme.onPrimary)).isAtLeast(4.5)
}
```

## 3. CI（ルールとコードの退行を見張る）

```yaml
# ① 単一ソースの再生成し忘れ／手編集（ドリフト）を検知
- run: npx rulesync generate --check   # 差分があれば exit 1
# ② accessibility テストを実機/エミュで
- run: ./gradlew connectedCheck
```

検証で見つかった粗は **`.rulesync/rules/*.md`（単一ソース）に反映 → 再生成**。1 か所直せば 3 形式すべてに伝播する（ルールが育つ）。
