---
nav_title: E メール検証
article_title: E メール検証
alias: "/email_validation/"
page_order: 3
page_type: reference
description: "この参考記事では、電子メールアドレスのローカル部分とホスト部分の検証ルールについて説明します。"
channel: email

---

# E メール検証

> この参考記事では、電子メールアドレスのローカル部分とホスト部分の検証ルールについて説明します。

検証は、ダッシュボードのメールアドレス、エンドユーザーのメールアドレス（顧客）、およびメールメッセージの送信元アドレスと返信先アドレスに使用されます。メール検証は、ユーザーのメールアドレスが更新された場合、API、CSV アップロード、SDK 経由で Braze にインポートされたり、ダッシュボードで変更されたりしたときに実行されます。メールアドレスに空白を含めることはできないことに注意してください。API を使用している場合、`400`空白はエラーになります。

Brazeは特定の文字を受け付けず、それらを無効と認識します。メールがバウンスされた場合、Braze はそのメールを無効としてマークし、購読ステータスは変更されません。  

{% details Accepted characters %}
-アルファベット (A-Z)
-数字 (0-9)
-シンボル
  - -
  - &#94;
  - +
  - $
  - '
  - &
  - #
  - /
  - %
  - *
  - =
  - `
  - |
  - ~
  - !
  - ?
	-。(文字または他の文字の間のみ)
{% enddetails %}

{% details Unaccepted characters %}
-ホワイトスペース (ASCII および Unicode)
{% enddetails %}

この検証を Briteverify のような検証サービスと混同しないでください。これは、メールアドレスの構文が正しいことを確認するためのチェックです。この検証プロセスを使用する主な理由の 1 つは、メールアドレスのローカル部分で国際文字 (UTF-8 など) を使用できるようにすることです。

メール構文検証は、メールアドレスのローカル部分とホスト部分の両方を調べます。ローカル部分はアスペランド (@) より前の部分で、ホスト部分はアスペランド (@) の後の部分です。たとえば、メールアドレスのこのローカル部分は、ピリオド (.) を除く任意の文字で開始および終了できます。このプロセスはメールアドレスの構文を検証するだけであり、ドメインに有効な MX サーバーがあるかどうか、またはリストされているドメインにユーザーが存在するかどうかは考慮されないことに注意してください。

{% alert note %}
[ドメイン部分にASCII以外の文字が含まれている場合は](https://en.wikipedia.org/wiki/ASCII)、[Brazeに提供する前にPunyCodeでエンコードする必要があります](https://www.punycoder.com/)。
{% endalert %}

Braze がユーザー追加のリクエストを受け取り、メールアドレスが無効と見なされた場合、API にエラーレスポンスが表示されます。CSV でアップロードすると、ユーザーは作成されますが、メールアドレスは追加されません。

## ローカルパーツ検証ルール

### 一般的なメール検証

ほとんどのドメインでは、ローカル部分は次のパラメーターに従う必要があります。
-Unicode 文字と数字を含む任意の文字、数字、および次の文字を使用できます:(+) (&) (#) (\_) (-) (^) または (/)
-次の文字を含めることはできますが、先頭または末尾に含めることはできません:(.)
-二重引用符 (「) を含めることはできません
-1 ～ 64 文字の長さでなければなりません


次の正規表現を使用して、電子メールアドレスが有効と見なされるかどうかを検証できます。
```
/\A([a-zA-Z0-9_\-\^+$'\&#\/!%\*=\?`\|~]|[[^\p{ASCII}\p{Space}]&&\p{Alnum}\p{Punct}\p{S}])(([a-zA-Z0-9_\-\^+$'\&#\/!%\*=\?`\|~\.]|[[^\p{ASCII}\p{Space}]&&\p{Alnum}\p{Punct}\p{S}])*([a-zA-Z0-9_\-\^+$'\&#\/!%\*=\?`\|~]|[[^\p{ASCII}\p{Space}]&&\p{Alnum}\p{Punct}\p{S}]))?\z/
```

{% alert important %}
ドメイン部分が Gmail アドレスの場合、ローカル部分の長さは 2 文字以上である必要があります。これは、このセクションに記載されている正規表現の検証に加えて行われます。
{% endalert %}

### マイクロソフトドメイン

ホストドメインに「msn」、「hotmail」、「outlook」、または「live」が含まれている場合は、次の正規表現を使用してローカル部分を検証します。 `/\A\w[\-\w]*(?:\.[\-\w]+)*\z/i`

Microsoft アドレスローカル部分は次のパラメーターに従う必要があります。

- 文字 (a～z)、アンダースコア (\_)、または数字 (0-9) で始めることができます。  
- 任意の英数字 (a～z または 0-9) またはアンダースコア (\_) を使用できます
- 次の文字を使用できます:(.)、(-)、(+) または (^)
- ピリオド (.) で始めることはできません
- 2 つ以上の連続したピリオド (.) を含めることはできません
- ピリオド (.) で終わることはできません

検証テストでは、「+」の前のローカル部分が正規表現と一致するかどうかがチェックされることに注意してください。

## ホストパーツ検証ルール

IPv4またはIPv6アドレスは、電子メールアドレスのホスト部分には使用できません。トップレベルドメイン (.com、.org、.netなど) は完全な数値ではない場合があります。

次の正規表現を使用してドメインを検証します。<br>
`/^[a-z\d](?:[a-z\d-]{0,61}[a-z\d])?(?:\.[a-z\d](?:[a-z\d-]{0,61}[a-z\d])?)+$/i`

ドメイン名は次のパラメータに従う必要があります。

- ピリオドで区切られた2つ以上のラベルで構成される
	- ドメイン名の各部分は「ラベル」と呼ばれます。たとえば、「example.com」というドメイン名は、「example」ラベルと「com」ラベルで構成されています。
- 少なくとも 1 つのピリオド (.) を含む必要があります
- 2 つ以上の連続したピリオドを含めることはできません
- ピリオドで区切られた各ラベルは次の条件を満たす必要があります。
	- 英数字 (a-z または 0-9) とハイフン (-) のみを含む
	- 英数字 (a ～ z または 0-9) で始まる
	- 英数字 (a～z または 0-9) で終わる
	- 1 ～ 63 文字を含む

### 追加の検証が必要です

ドメインの最終ラベルは有効なトップレベルドメイン (TLD) でなければならず、最後のピリオド (.) 以降で決定されます。[このTLDはICANNのTLDリストに含まれているはずです][2]。Braze メールバリデーターは、このセクションに記載されている正規表現に従ってメールの構文が正しいことのみをチェックします。入力ミスや存在しないアドレスは検出されません。

{% alert important %}
Unicodeは、メールアドレスのローカル部分にのみ使用できます。Unicodeはドメイン部分には使用できませんが、PunyCodeでエンコードされている可能性があります。
{% endalert %}

[2]: https://data.iana.org/TLD/tlds-alpha-by-domain.txt
