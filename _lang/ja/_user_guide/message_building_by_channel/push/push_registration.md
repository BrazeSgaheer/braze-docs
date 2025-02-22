---
nav_title: "プッシュ登録"
article_title: プッシュ登録
page_order: 2
page_type: reference
description: "この参考記事では、プッシュ登録の意味と、Brazeでのプッシュメッセージの送信方法、プッシュトークンの処理方法、プッシュ登録について説明します。"
channel:
  - push

---

# プッシュ登録

> この記事では、ユーザーにプッシュトークンが割り当てられるプロセスと、Braze がユーザーにプッシュメッセージを送信する方法について説明します。

## プッシュトークン

プッシュトークンとその内容を理解することは、ここBrazeでのプッシュメッセージの送信方法を理解するための基本的な部分です。プッシュトークンは、ユーザーのデバイスによって生成される一意の匿名識別子で、各受信者の通知の送信先を識別するために Braze に送信されます。デバイスにプッシュトークンがない場合、プッシュを送信する方法はありません。

プッシュトークンはプッシュサービスプロバイダーによって生成されます。Braze は Android 向けの Firebase クラウドメッセージングサービス（FCM）や iOS 向けの Apple プッシュ通知サービス（APN）などのプッシュサービスプロバイダーと接続し、これらのプロバイダーはアプリを識別する固有のデバイストークンを送信します。各デバイスには、メッセージングに使用される固有のトークンが 1 つあります。[トークンは期限切れになるか](#push-token-expire)、更新される可能性があります。

プッシュ通知をユーザーに送信する方法を理解するには、[プッシュトークン] [プッシュトークン] を分類する方法が2つあります。

1. **フォアグラウンドプッシュを使用すると**、ユーザーのデバイスのフォアグラウンドに定期的に表示されるプッシュ通知を送信できます。
2. **バックグラウンドプッシュは**、特定のデバイスがそのブランドからのプッシュ通知の受信をオプトインしているかどうかに関係なく利用できます。バックグラウンドプッシュにより、ブランドはサイレントプッシュ通知（意図的に表示されない通知）をデバイスに送信して、アンインストール追跡などの主要な機能をサポートできます。

ユーザープロファイルにアプリに関連する有効なフォアグラウンドプッシュトークンがある場合、Brazeはユーザーが特定のアプリに「プッシュ登録済み」と見なします。Brazeは、これらのユーザーを識別するのに役立つ特定のセグメンテーションフィルターを提供します。`Push enabled for App,`

{% alert note %}
`Push enabled for App`フィルターは、特定のアプリの有効なフォアグラウンドまたはバックグラウンドプッシュトークンの有無のみを考慮します。ただし、`Push Enabled`より一般的なフィルターでは、ワークスペース内のアプリのプッシュ通知を明示的に有効にしたユーザーをセグメント化します。この数にはフォアグラウンドプッシュのみが含まれ、登録を解除したユーザーは含まれません。これらのフィルターやその他のフィルターについて詳しくは、「[セグメンテーションフィルター]({{site.baseurl}}/user_guide/engagement_tools/segments/segmentation_filters)」を参照してください。
{% endalert %}

### 1 台のデバイスで複数のユーザー

プッシュトークンはデバイスとアプリの両方に固有であるため、同じデバイスを使用している複数のユーザーをプッシュトークンで区別することはできません。

たとえば、2 人のユーザーがいるとします。チャーリーとキムチャーリーがスマートフォンでアプリのプッシュ通知を有効にしていて、キムがチャーリーのスマートフォンを使用してチャーリーのプロフィールからログアウトし、自分のプロフィールにログインした場合、プッシュトークンはキムのプロフィールに再割り当てされます。その後、プッシュトークンは、キムがログアウトして Charlie が再度ログインするまで、そのデバイス上の Kim のプロファイルに割り当てられたままになります。

アプリまたはウェブサイトは、デバイスごとに1つのプッシュサブスクリプションしか設定できません。そのため、ユーザーがデバイスまたはウェブサイトからログアウトし、新しいユーザーがログインすると、プッシュトークンは新しいユーザーに再割り当てされます。これは、****エンゲージメントの連絡先設定セクションにあるユーザーのプロフィールに反映されます**** tab:

![ユーザープロファイルの\*\*エンゲージメント**タブにあるプッシュトークンの変更ログ。プッシュトークンが別のユーザーに移動された時期とトークンの内容が記載されています。] [4]

プッシュプロバイダー (APN/FCM) には 1 つのデバイス上の複数のユーザーを区別する方法がないため、最後にログインしたユーザーにプッシュトークンを渡して、デバイス上でプッシュの対象とするユーザーを決定します。

## プッシュトークンの登録

プラットフォームは、プッシュトークンの登録とプッシュ権限をさまざまな方法で処理します。

- **Android**:
  - **Android 13**:<br>プッシュ許可は、ユーザーが要求して付与する必要があります。ユーザーによって許可が与えられた後にトークンを受け取ります。アプリは必要に応じてユーザーに手動で許可をリクエストできますが、そうでない場合は、[アプリが通知チャネルを作成した後に](https://developer.android.com/reference/android/app/NotificationChannel)、ユーザーに自動的に許可を求めるメッセージが表示されます。
  - **アンドロイド 12 およびそれ以前**:<br>Braze が自動的にプッシュトークンをリクエストすると、`Subscribed`最初のセッションですべてのユーザーが対象となります。この時点で、**ユーザーはそのデバイスの有効なプッシュトークンでプッシュ対応になり**、デフォルトのサブスクリプション状態はになります`Subscribed`。<br><br>
- **iOS**:プッシュには自動的に登録されません。
    - **iOS 12 (仮認証あり)**:<br>[アプリは仮承認または承認済みプッシュをリクエストできます]({{site.baseurl}}/user_guide/message_building_by_channel/push/ios/notification_options/#provisional-push)。承認されたプッシュでは、通知を送信する前にユーザーからの明示的な許可が必要です（ユーザーが許可を得た後にトークンを受け取ります）。一方、[provisional push] [provisional-blog] では、__音やアラートなしで通知センターに静かに直接通知を送信できます__。
    - **iOS 11 以降および iOS 12 (仮認証なし)**:<br>プッシュ通知を受信するには、すべてのユーザーが明示的にオプトインする必要があります。ユーザーがオプトインした後にトークンを受け取ります。<br><br>
- **Web:**ネイティブブラウザの権限ダイアログを使用して、ユーザーに明示的なオプトインを要求する必要があります。ユーザーがオプトインした後にトークンを受け取ります。 アプリにいつでもパーミッションプロンプトを表示できる iOS や Android とは異なり、最近のブラウザーの中には、「ユーザージェスチャ」(マウスクリックまたはキーストローク) によってトリガーされた場合にのみプロンプトを表示するものがあります。サイトがページの読み込み時にプッシュ通知の許可をリクエストしようとすると、ブラウザによって無視されるか、サイレント状態になる可能性があります。

| プッシュトークンの取得 | プッシュトークンの送信 |
| ---------------- | ----------------- |
| アプリケーションがデバイスのプッシュトークンを取得するには、プッシュプロバイダーに登録する必要があります。| 開発者は、FCM/APNによって生成されたプッシュトークンを使用してデバイスをターゲットにできます。| |
{: .reset-td-br-1 .reset-td-br-2}

### ユーザーのプッシュ購読状態を確認

![プッシュサブスクリプションの状態が Subscribed に設定されている John Doe のユーザプロファイル。] [3]{: style="float:right;max-width:35%;margin-left:15px;"}

Braze でユーザーのプッシュサブスクリプションの状態を確認する方法は 2 つあります。

1. **ユーザープロフィール[ユーザー検索] [5] ページのBrazeダッシュボードから個々のユーザープロファイルにアクセスできます。ユーザーのプロフィールを（メールアドレス、電話番号、または外部ユーザーIDで）見つけたら、「**エンゲージメント**」タブを選択してユーザーのサブスクリプション状態を表示し、手動で調整できます。
<br>
2. **レスト API エクスポート**:[[セグメント別ユーザーまたは識別子エンドポイント別のユーザーエクスポートを使用して][identifier]、個々のユーザープロファイルを][segment] JSON 形式でエクスポートできます。Braze は、デバイスごとのプッシュ有効化情報を含むプッシュトークンオブジェクトを返します。

### プッシュ登録ステータスの確認

ユーザーのプロフィールの「**エンゲージメント**」タブには、「**プッシュ登録対象**」と表示され、その後にアプリ名が表示されます。そのデバイスのアプリ情報が存在しない場合は、2 つのダッシュ (**--**) が表示されます。ユーザーが所有するすべてのデバイスのエントリがあります。

デバイスエントリのアプリ名の前にが付いている場合`Foreground:`、アプリはそのデバイスでフォアグラウンドプッシュ通知 (ユーザーには表示) とバックグラウンドプッシュ通知 (ユーザーには非表示) の両方を受信することが許可されます。

![プッシュトークンの例を含む変更ログをプッシュする] [2]{: style="float:right;max-width:40%;margin-left:15px;margin-top:10px;"}

一方、デバイスエントリのアプリ名にプレフィックスが付いている場合`Background:`、[アプリはバックグラウンドプッシュの受信のみを許可され]({{site.baseurl}}/user_guide/message_building_by_channel/push/types/#background-push-notifications)、ユーザーに表示される通知をそのデバイスに表示することはできません。これは通常、ユーザーがそのデバイス上のアプリの通知を無効にしたことを示しています。

プッシュトークンを同じデバイス上の別のユーザーに移動すると、その最初のユーザーはプッシュ登録されなくなります。

## プッシュトークン管理

プッシュトークンの変更またはユーザープロファイルからの削除につながるアクションについては、次の表を確認してください。 

| アクション | 説明 |
| ------ | ----------- |
| `changeUser()` メソッド呼び出し | `changeUser()` Brazeメソッドは、SDKがユーザー行動データを割り当てているユーザーIDを切り替えます。このメソッドは通常、ユーザーがアプリケーションにログインしたときに呼び出されます。`changeUser()`が特定のデバイスで別のユーザーIDまたは新しいユーザーIDで呼び出されると、そのデバイスのプッシュトークンは、対応するユーザーIDの適切なBrazeプロファイルに移動されます。|
| プッシュ・エラーが発生する | トークンの削除につながる一般的なプッシュ・エラーには`MismatchSenderId`、`InvalidRegistration`、などのプッシュ・バウンスがあります。<br><br>[よくあるプッシュエラーの一覧をご覧ください][errors]。|
| ユーザーアンインストール | ユーザーがデバイスからアプリケーションをアンインストールすると、Braze はユーザーのプッシュトークンをプロファイルから削除します。|
{: .reset-td-br-1 .reset-td-br-2}

### これは大局的に見るとどのようなものでしょうか？

ユーザーが新しいアプリケーションを開き、プッシュプロンプトからのプッシュアクセスを許可すると、Braze SDK からプッシュプロバイダーへの呼び出しが行われます。その呼び出しが行われると、プッシュプロバイダーはすべてが正しく設定されているかどうかをチェックします。その場合、プッシュトークンがデバイスに渡されます。そのトークンが到着すると、SDK はこれを Braze に伝えます。Braze がプッシュプロバイダーからトークンを受け取ったら、ユーザープロファイルを更新または新規作成します。これらのユーザーは登録済みと見なされるようになりました。

キャンペーンを開始する場合は、プッシュペイロードを生成してプッシュプロバイダーに送信するキャンペーンを Braze で作成します。そこから、プロバイダーがプッシュペイロードをユーザーのデバイスに配信し、SDKがメッセージ状態を Braze に渡します。

![お客様の Braze と Apple プッシュ通知サービスまたは Firebase クラウドメッセージングとの間の前述のプッシュプロセスをマッピングしたフローチャート。][プッシュプロセス]

| 登録手順 | メッセージ手順 |
| ------------------ | --------------- |
| 1.顧客 (デバイス) がプッシュプロバイダーに登録する<br>2\.プロバイダーがプッシュトークンを生成して配信する<br>3\.Braze |1 のトークンをフラッシュします。Braze がプロバイダーにプッシュペイロードを送信<br>2\.プロバイダーがプッシュペイロードをデバイスに配信する<br>3\.SDKがメッセージング統計情報をBrazeに渡す |
{: .reset-td-br-1 .reset-td-br-2}

## よくある質問

### オプトインしたユーザーがアプリを削除してから再ダウンロードするとどうなりますか？

ユーザーがプッシュをオプトインし、プッシュメッセージを受け取り、後でアプリを削除したとします。これにより、デバイスレベルでのプッシュ同意が削除されます。ここから、アンインストール後に最初にバウンスされたプッシュにより、そのユーザーは今後のプッシュメッセージから自動的にオプトアウトされます。その後、ユーザーがアプリを再インストールしたが起動しなかった場合、アプリのプッシュトークンが再付与されていないため、Brazeはユーザーにプッシュを送信できなくなります。

さらに、ユーザーがフォアグラウンドプッシュを再度有効にする場合、プッシュメッセージの受信を開始するには、セッションを開始してユーザープロファイルのこの情報を更新する必要があります。
 
### プッシュトークンはいつ期限切れになりますか? {#push-token-expire}

残念ながら、APNとFCMはこれを実際には定義していません。プッシュトークンは、アプリが更新されたとき、ユーザーがデータを新しいデバイスに転送したとき、またはオペレーティングシステムを再インストールしたときに期限切れになることがあります。ほとんどの場合、プッシュプロバイダーが特定のプッシュトークンを期限切れにする理由については、実際にはわかりません。

このあいまいさを解消するために、SDKプッシュ統合では、常にセッション開始時にトークンを登録してフラッシュし、トークンを最新の状態に保つようにしています。

[errors]: {{site.baseurl}}/help/help_articles/push/push_error_codes/#push-bounced-mismatchsenderid
[identifier]: {{site.baseurl}}/api/endpoints/export/user_data/post_users_identifier/
[segment]: {{site.baseurl}}/api/endpoints/export/user_data/post_users_segment/
[プッシュプロセス]: {% image_buster /assets/img/push_process.png %}
[5]: {{site.baseurl}}/user_guide/engagement_tools/segments/using_user_search/
[2]: {% image_buster /assets/img/push_changelog.png %}
[プッシュトークン]: {{site.baseurl}}/user_guide/message_building_by_channel/push/push_registration/
[3]: {% image_buster /assets/img/push_example.png %}
[4]: {% image_buster /assets/img/push_token_changelog.png %}


