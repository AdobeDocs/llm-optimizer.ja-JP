---
title: Adobe Analytics との統合
description: Adobe AnalyticsとLLM Optimizerを連携して、AIを活用してサイトの発見、エンゲージメント、ビジネス成果を測定する方法を、リファラルトラフィックダッシュボードで説明します。
feature: Referral Traffic
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: a0b5a505-2fd7-4c3d-b61c-b557fb6f0558id: c0713b97-4af8-4c41-b742-5afcc6ced468id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2: id: e69d5a42-0217-4ca5-9396-a9a826a170da
topic_v2: id: c2be0313-b3ae-45e0-b454-d20bf54b23f2id: cdd65e7e-8839-44a2-bc21-0e03623b5dd1id: e1e0219c-f879-479f-8427-888ed2a6e9c2
autotag-review: '2026-04-30T18:25:39.169Z'
source-git-commit: 733c693f35c5b023681962cc5089492b95645b28
workflow-type: tm+mt
source-wordcount: 879
ht-degree: 4%

---


# Adobe Analytics との統合

Adobe Analyticsとの連携により、LLM Optimizerと自社のAdobe Analyticsデータを連携し、AIを活用した発見が、web サイトの実際のエンゲージメントとビジネスの成果にどのように結びつくのかを測定できます。 統合プロセスが完了すると、データは&#x200B;**リファラルトラフィック** ダッシュボードの&#x200B;**ビジネスへの影響** タブで利用できるようになります。

LLM Optimizerは、分析データとAIの可視化インサイトをリンクさせることで、次のことを追跡するのに役立ちます。

* AIが参照するページでのユーザーエンゲージメント。
* AI発見ジャーニーに関連するコンバージョンシグナル。
* GEO最適化のパフォーマンスへの影響。

この統合は、AIによる可視性の測定とビジネスパフォーマンス分析のギャップを埋めるのに役立ちます。 現在では、AIによる回答で企業がどこにあるかだけでなく、利用者が到着した後に何が起こるかを確認することができます。

## 対象 {#availability}

>[!IMPORTANT]
>
>Adobe Analyticsとの連携は、LLM Optimizerの有料オファーに含まれています。 無料体験版をご利用のお客様は、有料オファーにアップグレードするまで、Adobe Analyticsに接続できません。

## Adobe Analyticsをリファラルトラフィックダッシュボードに接続する {#connect}

接続フローは、[リファラルトラフィック](/help/dashboards/referral-traffic.md) ダッシュボードから次のように開始されます。

1. [リファラルトラフィック](/help/dashboards/referral-traffic.md) ダッシュボードを開きます。 デフォルトのビューは&#x200B;**トラフィックインサイト**&#x200B;です。

   ![リファラルトラフィックダッシュボード、「トラフィックインサイト」タブ ](/help/dashboards/assets/aa-integration-01-referral-traffic-traffic-insights.png)

1. 「**ビジネスへの影響**」タブを選択します。 分析プロバイダーが接続されていない場合は、**Connect to See Business Impact**&#x200B;と&#x200B;**Connect to Analytics**&#x200B;のバナーが表示されます。

   ![Analyticsへの接続を含む「ビジネスへの影響」タブ ](/help/dashboards/assets/aa-integration-02-business-impact-connect.png)

1. 「**Analyticsに接続**」を選択します。 これにより、**Analytics** タブの[顧客設定](/help/dashboards/customer-configuration.md) ダッシュボードが開きます。

   ![顧客設定、分析タブ ](/help/dashboards/assets/aa-integration-03-analytics-tab.png)

1. **資格情報**&#x200B;で、**クライアント ID**&#x200B;と&#x200B;**クライアントシークレット**&#x200B;を入力し、**検証と続行**&#x200B;を選択します。 次の点に注意してください。

   * **検証と続行**&#x200B;は、両方のフィールドが入力された場合にのみ使用できます。
   * 検証が成功すると、レポートスイートが読み込まれます。
   * 必要なレポートスイートにアクセスできる[ テクニカルアカウント ](https://developer.adobe.com/developer-console/docs/guides/authentication/ServerToServerAuthentication/)の&#x200B;**クライアント ID**&#x200B;と&#x200B;**クライアントシークレット**&#x200B;を使用します。

   ![Analyticsの資格情報を確認して続行](/help/dashboards/assets/aa-integration-04-credentials.png)

1. **設定**&#x200B;で、**レポートスイート**&#x200B;を選択します。

   レポートスイートを選択すると、LLM Optimizerはそのスイートで使用可能な&#x200B;**ページ URL Dimension**&#x200B;のオプションを読み込みます。

   ![ レポートスイートが選択され、ディメンションが読み込まれています](/help/dashboards/assets/aa-integration-05-report-suite.png)

1. **ページ URL Dimension** （スイート固有のディメンションリスト）を選択し、**保存して有効**&#x200B;を選択します。

   * **ページ URL Dimension**&#x200B;は、レポートスイートが選択され、ディメンションが読み込まれるまで無効のままです。
   * **保存して有効にする**&#x200B;は、ページ URL ディメンションを選択した後にのみ使用できます。

   ![ ページ URL ディメンションと保存して有効にする](/help/dashboards/assets/aa-integration-06-page-url-dimension.png)

1. 保存後、設定に&#x200B;**Connected** ステータスが表示されます。 **リファラルトラフィックダッシュボードに移動**&#x200B;すると、リファラルトラフィックダッシュボードに戻ることができます。 「**ビジネスへの影響**」タブの&#x200B;**リファラルトラフィック**&#x200B;で、ステータスが「**Adobe Analyticsに接続**」と表示されます。

   ![設定とビジネスへの影響でAdobe Analyticsに接続](/help/dashboards/assets/aa-integration-07-connected.png)

### つながった後 {#after-connect}

* LLM Optimizerでは、最後の4つの完全なカレンダー週&#x200B;**と、現在のカレンダー週**&#x200B;から日付&#x200B;**までをバックフィルします。**
* バックフィルの後、データは&#x200B;**日次**&#x200B;で更新され、**の前日**&#x200B;のプルが表示されます。

>[!NOTE]
>
>バックフィルが完了するには数時間かかる場合があります。

## 仕組み {#how-it-works}

### 設定

設定時に、LLM OptimizerがAdobe Analyticsの取り込みに使用するレポートスイートとページディメンションを定義します。 ページディメンションは、レポートスイートに応じて、標準の`variables/page` マッピングまたはカスタム `eVar`にすることができます。

### LLM トラフィックの識別方法

LLMが開始したトラフィックは、Adobe Analytics [ リファラータイプ – 対話型AI ツール ](https://experienceleague.adobe.com/en/docs/analytics/components/dimensions/referrer-type#conversational-ai-tools)を使用して識別されます。

### 取り込まれたデータ {#data-ingested}

次のデータは、LLM Optimizerによって取り込まれます。

**ディメンション**

* リファラードメイン
* リファラータイプ
* Country
* デバイスタイプ
* 選択したページディメンション

**指標**

* ページビュー
* 訪問回数
* 訪問者数
* エントリ数
* 終了数
* バウンス
* 単一ページ訪問
* 滞在時間
* 買い物かご
* 買い物かごへの追加
* 買い物かごからの削除
* 買い物かご表示
* チェックアウト
* 注文件数
* 売上高
* ユニット

### LLM Optimizerによるデータの活用

このデータセットは、次のようなLLM Optimizerのインサイトを強化します。

* ページレベルのLLM トラフィックパフォーマンス：
* LLM ソース間のリファラーパフォーマンス。
* 地域とデバイスレベルのトレンド。
* 下流のコマースの成果：

## よくある質問 {#faq}

質問：Adobe Analyticsとの連携は体験版中に利用できますか？

いいえ。 統合は、有料のLLM Optimizerのお客様のみが利用できます。

質問：どのようなデータが収集または保存されますか？

上記の「[取り込まれたデータ ](#data-ingested)」の章を参照してください。 LLM Optimizerは、生のヒットレベルのデータではなく、自社が承認したAdobe Analytics APIから集約された指標で動作します。

Q: データはどのように取り込まれますか？

Adobe Analytics APIのクエリを実行する権限をLLM Optimizerに付与しています。 LLM ソースに沿ったリファラルトラフィックは、これらのAPIを通じて消費されます。

質問：データはどのくらいの頻度で更新されますか？

データは毎日&#x200B;**更新されます（バックフィル完了後の前日まで）。**

質問：生のヒットレベルのデータはLLM Optimizerに保存されますか？

いいえ。 トラフィックパターンと傾向の把握には、**集計**&#x200B;指標のみが使用されます。

Q：完全なURL、クエリ文字列またはページコンテンツは保存されますか？

選択したページディメンションに使用される完全なURLを取り込むことができます。クエリ文字列とページコンテンツは、この統合には取り込まれません。

Q: ユーザーID （ECID、IP アドレス、Cookie ID）は保存されますか？

いいえ。

Q: データはどのくらいの期間保持されますか？

リテンションポリシーは変化する可能性があることに留意してください。 現在、データは無期限に保存されています。

Q：転送中および保存中のデータは暗号化されていますか？

現在、転送中のデータは暗号化されており、保存中のものではありません。 今後のアップデートで変更される可能性があります。

質問：過去のデータはバックフィルされていますか？

はい。 設定が成功すると、最後の4つの完全なカレンダー週と現在のカレンダー週がバックフィルされます。 [接続後](#after-connect)も参照してください。
