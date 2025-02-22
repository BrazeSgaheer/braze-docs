---
nav_title: キャンバスフローで開始する
article_title: キャンバスフローで開始する
page_order: 3
description: "この参照記事では、キャンバスフローで構築したキャンバスの開始前に準備してテストする方法について説明します。"
page_type: reference
tool: Canvas
---

# キャンバスフローで開始する

> この参照記事では、キャンバスフローで構築したキャンバスの開始前に準備してテストする方法について説明します。これには、キャンバスのエントリ条件、オーディエンスの概要、ユーザーセグメントなどの重要なキャンバスチェックポイントの特定が含まれます。

キャンバスの開始を準備する際は、キャンバスビルダーの各段階でキャンバスをチェックし、メッセージの送信に影響を与える可能性のある以下のような設定を確認することをお勧めします。
* [競合状態](#race-conditions)
* [配信時間](#delivery-times)
* [ユーザーセグメント](#segment-users)

## 競合状態 

キャンバスを開始する前に、発生する可能性のある[競合状態]({{site.baseurl}}/user_guide/engagement_tools/testing/race_conditions/)を検討してください。 

キャンバスに入るには、エントリスケジュールが発生する前にユーザーがエントリオーディエンスに含まれている必要があります。キャンバスがスケジュールされているか、アクションベースであるか、API によってトリガーされるかは関係ありません。 

![][1]{: style="max-width:75%;"}

キャンバスの開始後にエントリオーディエンスの対象となったユーザーは、キャンバスに入らないことに注意してください。

{% alert tip %}
スケジュール、アクションベース、API トリガーのいずれの配信をキャンバスで使用するかについてのガイダンスと詳細は、[エントリスケジュールタイプ]({{site.baseurl}}/user_guide/engagement_tools/canvas/create_a_canvas/create_a_canvas/#step-2b-set-your-canvas-entry-schedule)を参照してください。
{% endalert %}

### エントリオーディエンスフィルターを確認する

一般に、アクションベースまたは API によってトリガーされるキャンバスをオーディエンスフィルターと同じトリガーで構成することは避けてください。例えば、キャンバスの開始後は、特定のアクションを実行したユーザーがエントリオーディエンスに含まれるため、イベントをオーディエンスフィルターとして追加する必要はありません。 

オーディエンスのターゲティングに使用できるセグメンテーションフィルターについて詳しくは、「[セグメンテーションフィルター]({{site.baseurl}}/user_guide/engagement_tools/segments/segmentation_filters)」を参照してください。

### 複数の API リクエストを一括処理する

複数の呼び出しではなく、同じ API 呼び出しでリクエストし、ユーザープロファイルが最初に作成または更新されたことを確認します。その他の例については、「[複数のエンドポイントを使用する]({{site.baseurl}}/user_guide/engagement_tools/testing/race_conditions/#using-multiple-api-endpoints)」を参照してください。

### 遅延を追加する

競合状態を回避するもう 1 つのオプションは、キャンバスの最初のステップとして遅延ステップ (理想的には 5 分に設定) を使用することです。 

これにより、属性、メールアドレス、プッシュトークンが新しいユーザープロファイルに対して処理されてから、次のキャンバスステップの対象になる時間を確保できます。この遅延ステップがないと、メールがまだ更新されていないユーザーにメールが送信される可能性があります。

## 配信時間

キャンバスの配信時間をリアルタイムに設定すると、エンゲージメント率とコンバージョン率の向上につながります。キャンバスに設定した配信時間は書き留めておきましょう。エンゲージメント率とコンバージョン率を高めるには、キャンバスを定期的にトリガーするのではなく、リアルタイムでトリガーすることをおすすめします。

キャンバスの配信スケジュールを選択した場合、キャンバスを調整できるように、キャンバスを開始する少なくとも 24 時間前にキャンバスをスケジュールすることをお勧めします。

## ユーザーセグメント

キャンバスフローのユーザージャーニーをコンポーネントで飽和状態にする前に、ユーザージャーニーをシンプルに保つ方法を検討してください。キャンバスエディターの簡略化されたビューを使用すると、ユーザージャーニーがどのように分岐するかをよりよく理解できます。 

シンプルかつ効果的な方法でユーザーをセグメント化するために使用できる主なコンポーネントは 4 つあります。

* [オーディエンスパス](#audience-paths)
* [条件分岐](#decision-split)
* [アクションパス](#action-paths)
* [実験パス](#experiment-paths)

### オーディエンスパス

[オーディエンスパス]({{site.baseurl}}/user_guide/engagement_tools/canvas/canvas_components/audience_paths/)ステップを使用して、カスタム属性、カスタムイベント、およびユーザープロファイルからの以前のメッセージエンゲージメントデータに基づいてキャンバス内のユーザーをセグメント化します。

### 条件分岐

[条件分岐]({{site.baseurl}}/user_guide/engagement_tools/canvas/canvas_components/decision_split/)ステップでは、正反対の質問に対する回答に基づいて、ユーザーをさまざまなユーザージャーニーパスに誘導できます。

### アクションパス

[アクションパス]({{site.baseurl}}/user_guide/engagement_tools/canvas/canvas_components/action_paths/)は、カスタムイベント、購入イベント、カスタム属性の変更などのリアルタイムの行動に基づいてユーザーをセグメント化することを重視しています。 

### 実験パス

アクションパスと同様に、キャンバスの[実験パス]({{site.baseurl}}/user_guide/engagement_tools/canvas/canvas_components/experiment_paths/)ステップを活用して、コントロールグループとともに複数のキャンバスパスを相互にテストできます。これにより、パスのパフォーマンスが追跡され、キャンバスジャーニーを構築する際に情報に基づいた意思決定を行うことができます。 

## 開始前のテスト

キャンバスの詳細を確認したら、「[テストキャンバスを送信する]({{site.baseurl}}/user_guide/engagement_tools/canvas/testing_canvases/sending_test_canvases/)」を参照し、テストユーザーでキャンバスをテストするために活用できるさまざまな方法を確認してください。

## トラブルシューティング

{% details Why are my users not receiving my Canvas messages? %}
\- プッシュサブスクリプションの状態が「サブスクライブ済み」または「オプトイン済み」で、**かつ****プッシュの有効化**ステータスが「true」に設定されていることを確認します。これらをキャンバスエントリルールとして追加した場合、キャンバスに入ってからメッセージステップを受信するまでの間に、ユーザーが登録を解除した可能性があります。
\- キャンバスでグローバルフリークエンシー キャップが有効になっている場合、特定のルールに応じて、各ユーザーが特定のチャネルからメッセージを受信する回数を制限できます。
\- サイレント時間が有効になっていると、メッセージの送信時間に影響する可能性があります。この場合は、次に利用可能な時間 (サイレント時間の終了時) にメッセージが送信されたり、メッセージが完全にキャンセルされることがあります。
{% enddetails %}

[1]: {% image_buster /assets/img_archive/launch_with_canvas_flow_example.png %}
