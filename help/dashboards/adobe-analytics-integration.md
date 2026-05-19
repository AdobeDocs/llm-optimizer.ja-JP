---
title: Adobe Analytics との統合
description: Adobe Analytics を LLM Optimizer と接続して、リファラルトラフィックダッシュボードで AI 駆動型の検出、サイトエンゲージメント、ビジネス成果を測定する方法について説明します。
feature: Referral Traffic
autotag-review: '2026-05-15T17:29:50.263Z'
TQID: 'https://experienceleague.adobe.com/Wo-7p-mNQRrpS3EhmnS38UF1-IcaKEJpUz5H3BwY8Yo'
product_v2:
  - id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2:
  - id: c0713b97-4af8-4c41-b742-5afcc6ced468
  - id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2:
  - id: e69d5a42-0217-4ca5-9396-a9a826a170da
topic_v2:
  - id: c2be0313-b3ae-45e0-b454-d20bf54b23f2
  - id: e1e0219c-f879-479f-8427-888ed2a6e9c2
  - id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 7a92587197cf6a9eec6b01bd4eaeeaf1194d3088
workflow-type: tm+mt
source-wordcount: 879
ht-degree: 100%

---


# Adobe Analytics との統合

Adobe Analytics との統合により、LLM Optimizer は組織の Adobe Analytics データと接続され、AI 駆動型の検出が実際の web サイトエンゲージメントやビジネス成果に結びつく仕組みを測定できます。 統合プロセスが完了すると、データは「**ビジネスへの影響**」タブの&#x200B;**リファラルトラフィック**&#x200B;ダッシュボードに表示されます。

LLM Optimizer は、Analytics データと AI 可視性インサイトをリンクさせることで、次を追跡するのに役立ちます。

* AI が参照したページでのユーザーエンゲージメント。
* AI 検出ジャーニーに関連したコンバージョンシグナル。
* GEO 最適化のパフォーマンスへの影響。

この統合により、AI 可視性測定とビジネスパフォーマンス分析の間のギャップが埋められます。 チームは、AI の応答でブランドが表示される場所のみでなく、ユーザーが到着した後に発生する内容も確認できるようになりました。

## 対象 {#availability}

>[!IMPORTANT]
>
>Adobe Analytics との統合は、LLM Optimizer の有料オファーに含まれています。 無料体験版を使用しているお客様は、有料オファーにアップグレードするまで Adobe Analytics に接続できません。

## リファラルトラフィックダッシュボードへの Adobe Analytics の接続 {#connect}

接続フローは、[リファラルトラフィック](/help/dashboards/referral-traffic.md)ダッシュボードから次のように開始されます。

1. [リファラルトラフィック](/help/dashboards/referral-traffic.md)ダッシュボードを開きます。 デフォルトのビューは&#x200B;**トラフィックインサイト**&#x200B;です。

   ![リファラルトラフィックダッシュボード、「トラフィックインサイト」タブ](/help/dashboards/assets/aa-integration-01-referral-traffic-traffic-insights.png)

1. 「**ビジネスへの影響**」タブを選択します。 Analytics プロバイダーが接続されていない場合は、**ビジネスへの影響を確認に接続**&#x200B;というバナーが **Analytics に接続**&#x200B;と共に表示されます。

   ![Analytics に接続を含む「ビジネスへの影響」タブ](/help/dashboards/assets/aa-integration-02-business-impact-connect.png)

1. 「**Analytics に接続**」を選択します。 これにより、「**Analytics**」タブの[顧客設定](/help/dashboards/customer-configuration.md)ダッシュボードが開きます。

   ![顧客設定、「Analytics」タブ](/help/dashboards/assets/aa-integration-03-analytics-tab.png)

1. **資格情報**&#x200B;で、**クライアント ID** と&#x200B;**クライアント秘密鍵**&#x200B;を入力し、「**検証して続行**」を選択します。 次の点に注意してください。

   * **検証して続行**&#x200B;は、両方のフィールドが入力された場合にのみ使用できます。
   * 検証が成功すると、レポートスイートが読み込まれます。
   * 必要なレポートスイートに対するアクセス権を持つ[テクニカルアカウント](https://developer.adobe.com/developer-console/docs/guides/authentication/ServerToServerAuthentication/)の&#x200B;**クライアント ID** と&#x200B;**クライアント秘密鍵**&#x200B;を使用します。

   ![Analytics 資格情報の検証して続行](/help/dashboards/assets/aa-integration-04-credentials.png)

1. **設定**&#x200B;の下で、「**レポートスイート**」を選択します。

   レポートスイートが選択されると、LLM Optimizer はそのスイートで使用可能な「**ページ URL ディメンション**」オプションを読み込みます。

   ![レポートスイートが選択され、ディメンションが読み込まれています](/help/dashboards/assets/aa-integration-05-report-suite.png)

1. 「**ページ URL ディメンション**」（スイート固有のディメンションリスト）を選択し、「**保存して有効にする**」を選択します。

   * レポートスイートが選択され、ディメンションが読み込まれるまで、**ページ URL ディメンション**&#x200B;は無効のままです。
   * **保存して有効にする**&#x200B;は、ページ URL ディメンションを選択した後にのみ使用できます。

   ![「ページ URL ディメンション」と「保存して有効にする」](/help/dashboards/assets/aa-integration-06-page-url-dimension.png)

1. 保存後、設定には&#x200B;**接続済み**&#x200B;ステータスが表示されます。 **リファラルトラフィックダッシュボードに移動**&#x200B;を使用すると、リファラルトラフィックダッシュボードに戻ることができます。 「**ビジネスへの影響**」タブの&#x200B;**リファラルトラフィック**&#x200B;では、ステータスが **Adobe Analytics に接続済み**&#x200B;と表示されます。

   ![設定での「Adobe Analytics に接続済み」と「ビジネスへの影響」](/help/dashboards/assets/aa-integration-07-connected.png)

### 接続後 {#after-connect}

* LLM Optimizer は、**過去 4 週間の完全なカレンダー週**&#x200B;と&#x200B;**現在までのカレンダー週**&#x200B;をバックフィルします。
* バックフィル後、データは&#x200B;**前日全体**&#x200B;のデータを取得して&#x200B;**毎日**&#x200B;更新されます。

>[!NOTE]
>
>バックフィルには数時間かかる場合があります。

## 仕組み {#how-it-works}

### 設定

設定時に、LLM Optimizer が Adobe Analytics のデータ取り込みに使用するレポートスイートとページディメンションを定義します。 ページディメンションは、レポートスイートに応じて、標準 `variables/page` マッピングまたはカスタム `eVar` のいずれかになります。

### LLM トラフィックの特定方法

LLM で発生したトラフィックは、Adobe Analytics の[リファラータイプ - 対話型 AI ツール](https://experienceleague.adobe.com/ja/docs/analytics/components/dimensions/referrer-type#conversational-ai-tools)を使用して特定されます。

### 取り込まれたデータ {#data-ingested}

次のデータは、LLM Optimizer で取り込まれます。

**ディメンション**

* リファラードメイン
* リファラータイプ
* Country
* デバイスタイプ
* 選択されたページディメンション

**指標**

* ページビュー
* 訪問回数
* 訪問者数
* エントリ数
* 終了数
* バウンス
* 単一ページ訪問数
* 滞在時間
* 買い物かご数
* 買い物かごへの追加数
* 買い物かごからの削除数
* 買い物かご表示数
* チェックアウト回数
* 注文件数
* 売上高
* 購入点数

### LLM Optimizer がこのデータを使用する仕組み

このデータセットは、LLM Optimizer の次のインサイトを強化します。

* ページレベルの LLM トラフィックパフォーマンス。
* LLM ソース間のリファラーパフォーマンス。
* 地域とデバイスレベルのトレンド。
* ダウンストリームのコマースの成果。

## よくある質問 {#faq}

Q：Adobe Analytics との統合は、体験版期間中も使用できますか？

いいえ。 統合は、LLM Optimizer の有料顧客のみが使用できます。

Q：収集または保存されるデータは何ですか？

上記の[取り込まれたデータ ](#data-ingested)の章を参照してください。 LLM Optimizer は、生のヒットレベルのデータではなく、組織により承認された Adobe Analytics API からの集計済み指標を使用して動作します。

Q：データはどのように取り込まれますか？

組織は、LLM Optimizer が Adobe Analytics API にクエリを実行することを承認します。 LLM ソースに沿ったリファラルトラフィックは、これらの API を通じて消費されます。

Q：データが更新される頻度はどれくらいですか？

データは&#x200B;**毎日**&#x200B;更新されます（バックフィル完了後の前日まで）。

Q：生のヒットレベルのデータは LLM Optimizer に保存されますか？

いいえ。 トラフィックのパターンやトレンドを把握するには、**集計済み**&#x200B;指標のみが使用されます。

Q：完全な URL、クエリ文字列またはページコンテンツは保存されますか？

選択したページディメンションに使用される完全な URL を取り込むことができます。クエリ文字列とページコンテンツは、この統合では取り込まれません。

Q：ユーザーID （ECID、IP アドレス、Cookie ID）は保存されますか？

いいえ。

Q：データが保持される期間はどれくらいですか？

保持ポリシーは進化する場合があることに留意してください。 現在、データは無期限に保存されます。

Q：データは転送中および保存時に暗号化されていますか？

現在、データは保存時ではなく転送中に暗号化されます。 今後のアップデートで変更される場合があります。

Q：履歴データはバックフィルされますか？

はい。 設定が正常に完了すると、過去 4 週間の完全なカレンダー週と現在のカレンダー週がバックフィルされます。 [接続後](#after-connect)も参照してください。
