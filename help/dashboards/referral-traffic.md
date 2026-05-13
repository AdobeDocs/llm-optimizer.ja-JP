---
title: リファラルトラフィック
description: リファラルトラフィックダッシュボードを使用して、外部プラットフォーム、AI の引用、リファラルリンクから訪問者がサイトに到達する方法を確認する方法について説明します。
feature: Referral Traffic
source-git-commit: ef3ef2c8f353047925c4fc99c73d9cf0399f24ca
workflow-type: tm+mt
source-wordcount: '750'
ht-degree: 78%

---


# リファラルトラフィック

リファラルトラフィックでは、外部プラットフォーム、AI の引用、リファラルリンクから訪問者がサイトに到達する方法が表示されます。 外部の web サイトやプラットフォームからのトラフィックソース、リファラルパターン、コンバージョン指標を追跡および分析します。 これは、最も関与の多いトラフィックを推進するソース、地域、ページを理解するのに役立ちます。<!--Data is sourced from the CDN logs, a privacy-preserving source that does not capture personal user data.--> また、表示するデータを絞り込むのに役立つカスタマイズ可能なフィルターもあります。

>[!NOTE]
>デフォルトでは、このダッシュボードは&#x200B;**CDN ログ**&#x200B;からトラフィックインサイトを構築します。 有料オファーを利用している場合は、**Adobe Analytics**&#x200B;または&#x200B;**Google Analytics 4** （GA4）に接続して、AI主導の発見とサイトエンゲージメントを測定するデータを追加できます。 このデータは、**ビジネスへの影響** タブで利用できます。 Adobe AnalyticsまたはGA4との統合がなければ、タブには入力されないことに注意してください。 詳細については、[Adobe Analytics統合](/help/dashboards/adobe-analytics-integration.md)または[Google Analytics統合](/help/dashboards/google-analytics-integration.md)を参照してください。

![リファラルページ](/help/dashboards/assets/referral-traffic.png)

このページでは次の内容について説明します。

* [設定](#setup)
* [フィルター](#filters)
* [全体的なリファラルパフォーマンス](#overall-performance)
* [上位のリファラル URL](#top-referrals)
* [リファラルトラフィックの詳細](#traffic-details)

[ ブランド中心のエクスペリエンス ](/help/overview/quick-start.md#brand-centric-experience)を利用している場合は、**リファラルトラフィック**&#x200B;に移動し、LLM リファラルトラフィックのインサイトを表示するサイトを選択します。

![リファラルトラフィック — サイトセレクター（ブランド中心のエクスペリエンス） ](/help/assets/brand-centric-experience/referral-traffic-dashboard.png)

## 設定 {#setup}

初回ログイン時に、リファラルトラフィックダッシュボードが空白で表示される場合があります。 データを表示するには、CDN ログ転送を設定する必要があります。

[ ブランド中心のエクスペリエンス ](/help/overview/quick-start.md#brand-centric-experience)を利用しているお客様の場合、**ブランド管理**&#x200B;に移動し、**CDN** ラベルをクリックすると、CDN ログ転送情報を追加できます。

**顧客設定（クラシックエクスペリエンス）:** **設定に移動**&#x200B;を選択して、[CDN ログ転送](/help/dashboards/customer-configuration.md#cdn-configuration)を設定します。

![リファラル設定](/help/dashboards/assets/referral-setup1.png)

<!--
1. Select your Source (either CDN logs or AEM Operational Telemetry).
2. Enter a primary contact email.
3. Click **Request activation** to enable data ingestion. Hiding this until confirmation from PM
-->

アクティブ化すると、ダッシュボードにリファラルトラフィック指標が入力されます。

## フィルター {#filters}

ページの上部でフィルターを適用して表示を絞り込むことができます。 選択するフィルターは、ダッシュボードに存在する&#x200B;**すべて**&#x200B;のセクションに影響を与えます。 次の項目をカスタマイズできます。

* **日付範囲** - 表示するデータの時間枠を選択します。 例：過去 4 週間。 また、「**カスタム週**」オプションを選択して期間をカスタマイズするオプションもあります。
* **プラットフォーム** - Google、OpenAI、ソーシャルメディアなどの特定のトラフィックソースを選択します。
* **ページインテント** - ユーザーのインテント別にリファラルトラフィックをフィルタリングします。
* **チャネルソース** - チャネルのソースでフィルタリングします。 オプションには、LLM、獲得、有料、混合のリファラルチャネルが含まれます。
* **デバイスタイプ** - 訪問者のデバイスタイプ（デスクトップ、モバイル、すべてのデバイス）別にトラフィックを分析します。
* **地域** - 様々な地域をまたいでリファラルパターンを表示します。

目的のフィルターを選択したら、「**フィルターを適用**」をクリックして、選択をダッシュボードに適用します。

## 全体的なリファラルパフォーマンス {#overall-performance}

ダッシュボードには、次を含む主要指標が表示され、全体的なリファラルパフォーマンスがハイライト表示されます。

* **リファラルトラフィックの合計数** - すべてのソースからのリファラルトラフィックの合計数。
* **LLM からのリファラルトラフィック数** - LLM からのリファラルトラフィックの合計数。
* **同意率** - 同意プロンプトを受け入れる訪問者の割合。
* **バウンス率** - エンゲージメントイベントが発生しなかったリファラルソースからのセッションの割合。

![リファラルページ](/help/dashboards/assets/referral-traffic.png)

上記の全体的なパフォーマンス指標の他に、様々なマーケット、リファラルソース、ページインテントカテゴリをまたいでトラフィック分布を示す 3 つの追加パネルがあります<!-- the **Top Regions** panel breaks down traffic by geography. Meanwhile, the **Top Referral Sources** panel shows the platforms driving the most visits. Trend indicators for the metrics show how these values are changing over time compared to the previous period.-->

<!--
## Top Referral URLs {#top-referrals}

The Top Referral URLs list surfaces your site's most visited pages from referrals.

![Top Referral URLs](/help/dashboards/assets/top-url.png)
-->

## リファラルソースの詳細と URL パフォーマンス分析 {#traffic-details}

リファラルソースの詳細と URL パフォーマンス分析テーブルは、トラフィック量と品質の両方を評価するのに役立ちます。 詳しくは、次の各タブをクリックしてください。

![リファラルトラフィックの詳細](/help/dashboards/assets/traffic-details.png)

>[!BEGINTABS]

>[!TAB リファラルソースの詳細]

リファラルソースの詳細ビューでは、OpenAI、Microsoft、Google、Perplexity など、様々なプラットフォームからのトラフィックが分類されます。 訪問数、バウンス率、チャネルタイプなどの主要指標が表示されるので、サイトに最も関与しているトラフィックを推進している AI および検索ソースを理解するのに役立ちます。

* **ソース** - リファラルトラフィックのソース。
* **訪問回数** - 各ソースの訪問回数の合計数。
* **バウンス率** - エンゲージメントイベントが発生しなかったリファラルソースからのセッションの割合。
* **チャネル** - ソースのチャネル（獲得、有料またはその両方）。

>[!TAB URL パフォーマンス分析]

URL パフォーマンス分析ビューでは、LLM やその他のソースからのリファラルトラフィック量に基づいて、パフォーマンスが上位のページがランク付けされます。 トラフィック、バウンス率、同意率、ページインテントなどの指標をハイライト表示し、AI 駆動型のリファラルから最もエンゲージメントの高い訪問者を引き付け、保持するページを特定するのに役立ちます。 テーブルには、トピックにすばやくアクセスする検索フィールドがあります。

>[!ENDTABS]

両方のテーブルで、「**書き出し**」オプションを使用してテーブル .csv をダウンロードし、インサイトをチームと共有したり、テーブルをエグゼクティブレポートに含めたりすることができます。 さらに、両方のテーブルについて、「**列を設定**」ボタンをクリックすると、表示する指標をカスタマイズできます。
