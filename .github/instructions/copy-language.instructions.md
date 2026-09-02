---
description: 言葉づかい。平易な日本語・短文・動詞で、専門用語や英語を避ける。UI文言/ラベル/メッセージを書くときに適用。
applyTo: '**/*.kt,**/*.kts'
---
# コピー・言葉づかい（手引き：迷わせない）

読めない・分からない言葉は、無いのと同じ。誰でも読める言葉にする。

## must

- **平易な日本語・短文**。専門用語・英語・略語を避ける（例：`Submit` → 「記録する」）。
- ボタンは**動詞**で「何が起きるか」を書く（「OK」より「保存する」）。
- ラベルは意味を持たせ、`contentDescription` と表示文言を一致させる。

## nice

- 一文一義。長い説明は分割する。
- 入力欄は用途に合うキーボードを出す：`KeyboardOptions(keyboardType = KeyboardType.Email, imeAction = ImeAction.Done)`（メール・数値・電話など＝入力ミスと手間を減らす）。
- 個人情報の入力欄は Autofill を有効化：`Modifier.semantics { contentType = ContentType.EmailAddress }`（再入力の負担を減らす）。

## Compose

```kotlin
// ボタンは動詞で「何が起きるか」を書く（表示文言＝読み上げ文言）
OutlinedButton(onClick = onSend) { Text("メールで送る") }   // 「送信」でなく動詞

// 本文中のリンクは生 clickable でなく LinkAnnotation＝TalkBack が「リンク」と認識・フォーカスできる
val text = buildAnnotatedString {
    append("使い方は")
    withLink(
        LinkAnnotation.Url(
            "https://example.com/help",
            TextLinkStyles(SpanStyle(textDecoration = TextDecoration.Underline)),
        ),
    ) { append("選び方ガイド") }
    append("を参照。")
}
Text(text)
```

## 検証

- 主要文言を音読し、初見で意味が取れるかレビュー。TalkBack 読み上げが自然か確認。
