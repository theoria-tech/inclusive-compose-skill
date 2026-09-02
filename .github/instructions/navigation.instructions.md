---
description: ナビゲーションと戻る安心。現在地・次の一歩を明示し、戻っても失わせない。画面遷移/フォーム/戻る操作を扱うときに適用。
applyTo: '**/*.kt,**/*.kts'
---
# ナビゲーション（手引き 原則①②）

「記憶に頼らず行動できる」「安心して戻れる」を UI で実現する。

## must

- 現在地と「次に何をするか」を常に画面に出す（覚えさせない）。
- 入力途中を失わせない＝**自動で下書き保存**する。「破棄しますか？」の二択ダイアログに頼らない。
- **予測的戻る（Predictive Back）を妨げない**（既定有効：Android 16 / targetSdk 36 以降）。

## nice

- 多段フォームはステッパーで現在地を示し、各ステップ名を読み上げ可能にする。
- 離脱経路（戻る・キャンセル）を進むボタンと同じ視認性で置く。片方だけを目立たせて誘導しない。
- 削除・退会のような取り返しのつかない操作は、うっかり押せないようにして、実行前に確認をはさむ。入力途中の破棄はこれと別で、上の must のとおり自動で下書き保存する（手引き 視点E：さりげない安全）。
- 画面遷移級の変化は `paneTitle` を付与。
- 入力欄は用途に合うキーボードを出す：`KeyboardOptions(keyboardType = KeyboardType.Email, imeAction = ImeAction.Done)`（メール・数値・電話など＝入力ミスと手間を減らす）。テキストフィールドに付ける。
- 個人情報の入力欄は Autofill を有効化：テキストフィールドに `Modifier.semantics { contentType = ContentType.EmailAddress }`（再入力の負担を減らす）。パスワードは `BasicSecureTextField`（難読化＋読み上げ保護）、低レベルには `Modifier.semantics { password() }`。
- 長いフォームはタブ移動順を整える：`FocusRequester.createRefs()` ＋ 各欄に `Modifier.focusProperties { next = … }`（親に `Modifier.focusGroup()`）。フォーカスした欄が IME に隠れるなら `bringIntoViewRequester`。
- インラインのエラーには入力焦点を移すと、スイッチアクセス/キーボード操作で見落とさせない（下記 ## Compose・読み上げは `error-handling` の `liveRegion` が担う）。

## Compose

```kotlin
LaunchedEffect(formState) { draftRepository.saveDebounced(formState) } // 自動下書き
// 既定：戻るに介入しない → 予測的戻るアニメがそのまま出る。下書きは残る。
// 戻り進捗に合わせて UI を動かす等、介入が要る場合のみ PredictiveBackHandler（activity-compose 1.8+）
PredictiveBackHandler(enabled = hasDraft) { progress: Flow<BackEventCompat> ->
    try { progress.collect { e -> /* e.progress: 0f..1f で UI 追従 */ }; onConfirmedBack() }
    catch (e: CancellationException) { /* ジェスチャ取消：UI を戻す */ } // import kotlin.coroutines.cancellation.CancellationException
}
// タップの意味は clickable(onClickLabel = "…") で読み上げに乗せる
```

```kotlin
// インラインのエラーへ入力焦点を移す（キーボード/スイッチ操作向け・Text は focusable 必須）
val fr = remember { FocusRequester() }
if (showError) Text(errorMsg, Modifier.focusRequester(fr).focusable())
LaunchedEffect(showError) { if (showError) fr.requestFocus() } // key 必須。AlertDialog は自前でフォーカストラップ済みなので不要
```

## 検証

- 戻る→再入場で下書きが残ることを実機で確認。予測的戻るアニメが出ることを確認。
