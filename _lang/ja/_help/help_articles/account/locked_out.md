---
nav_title: アカウントからロックアウト
article_title: アカウントからロックアウト
page_order: 0

page_type: solution
description: "このヘルプ記事では、Brazeアカウントからロックアウトされた場合のトラブルシューティング手順を説明します。"
tool: Dashboard
---

# アカウントからロックアウトされました

Brazeアカウントからロックアウトされているので、心配はいりません。私たちはあなたが戻ってくるのを手伝うことができます。	

発生しているロックアウトの種類は、表示されるエラー メッセージで判断できます。	

- [パスワードに関するエラーが表示されます。](#password-error)	
- [エラーは見当たりませんが、Brazeはまだ私を中に入れません。](#instance-error)	
- [アカウントの強制停止に関するエラーが表示されます。](#account-suspension)	

## パスワードエラー

お客様のアカウントのセキュリティは当社にとって重要であるため、Brazeアカウントにログインするにはパスワードが必要です。	
\- 正しい [Brazeダッシュボードインスタンス][1]にログインしていることを確認します。アカウント管理者またはBrazeアカウントマネージャーに確認してください。	
\- パスワードの有効期限が切れている可能性があるため、 [パスワードを再設定][2]する必要があります。	
- [シングルサインオン][3] サービスを使用している場合は、設定が正しく完了していることをアカウント管理者に確認してください。	
\- 会社でBrazeを複数使用している場合は、間違ったメールアドレスを使用してログインしている可能性があります。  	

疑わしい場合は、いつでも [パスワードをリセット][2]できます。	

## インスタンスエラー

通常ログインしているのと同じマシンを使用している場合、Brazeは正しいインスタンスを自動的に検出します。ただし、表示されない場合、または初めてログインする場合は、次の点を考慮することをお勧めします。	

- 正しい [Brazeダッシュボードインスタンス][1]にログインしていることを確認してください。アカウント管理者またはBrazeアカウントマネージャーに確認してください。
- あなたの会社がBrazeの複数のインスタンスを使用している場合、ログインに間違った電子メールを使用している可能性があります。	

## アカウントの強制停止	

これは頻繁には発生しませんが、アカウントの停止と削除を非常に深刻に受け止めています。このエラーを見つけた場合は、会社のBraze管理者、Brazeアカウントマネージャー、または [Brazeサポート][support]に連絡するのが最善の策です。

_最終更新日:2019年10月19日_

[support]: {{site.baseurl}}/support_contact/	
[1]: {{site.baseurl}}/user_guide/administrative/access_braze/braze_instances/#braze-instances
[2]: {{site.baseurl}}/user_guide/administrative/access_braze/accessing_your_account/#resetting-your-password
[3]: {{site.baseurl}}/user_guide/administrative/access_braze/single_sign_on/set_up/
