---
title: リファラルトラフィック
description: リファラルトラフィックダッシュボードを使用して、外部プラットフォーム、AI の引用、リファラルリンクから訪問者がサイトにどのように到達するかを確認する方法を説明します。
feature: Referral Traffic
source-git-commit: e50c87e8e5a669526f3c10855c1629ce82b67aef
workflow-type: tm+mt
source-wordcount: '603'
ht-degree: 0%

---


# リファラルトラフィック

リファラルトラフィックは、外部プラットフォーム、AI 引用、リファラルリンクから訪問者がお客様のサイトに到達する方法を示します。 外部の Web サイトやプラットフォームからのトラフィックソース、リファラルパターン、コンバージョン指標を追跡および分析します。 これにより、最も関与の多いトラフィックを推進するソース、地域、ページを理解できます。 <!--Data is sourced from the CDN logs, a privacy-preserving source that does not capture personal user data.--> 表示されるデータを絞り込むのに役立つ、カスタマイズ可能なフィルターもあります。

![&#x200B; リファラルページ &#x200B;](/help/dashboards/assets/referral-traffic.png)

このページの詳細は次のとおりです。

* [設定](#setup)
* [フィルター](#filters)
* [リファラルパフォーマンス全体](#overall-performance)
* [上位のリファラル URL](#top-referrals)
* [リファラルトラフィックの詳細](#traffic-details)

## 設定 {#setup}

初回ログイン時に、リファラルトラフィックダッシュボードが空白で表示される場合があります。 データを表示するには、「[&#x200B; 設定に移動 &#x200B;](/help/dashboards/customer-configuration.md#cdn-configuration)」を選択して **CDN ログ転送** を設定する必要があります。

![&#x200B; リファラルの設定 &#x200B;](/help/dashboards/assets/referral-setup1.png)

<!--- 1. Select your Source (either CDN logs or AEM Operational Telemetry).
2. Enter a primary contact email.
3. Click **Request activation** to enable data ingestion. Hiding this until confirmation from PM-->

アクティブ化すると、ダッシュボードにリファラルトラフィック指標が入力されます。

## フィルター {#filters}

ページの上部で、フィルターを適用してビューを絞り込むことができます。 選択するフィルターは、ダッシュボードに存在するセクション **すべて** に影響します。 次の項目をカスタマイズできます。

* **日付範囲** – 表示されたデータの時間枠を選択します。 例：過去 4 週間。 また、「カスタム週 **オプションを選択して期間をカスタマイズするオプションもあり** す。
* **Platform** - Google、OpenAI、ソーシャルメディアなど、特定のトラフィックソースを選択します。
* **ページインテント** - ユーザーインテントでリファラルトラフィックをフィルタリングします。
* **チャネルSource** - チャネルのソースでフィルタリングします。 オプションには、LLM、獲得、有料、または混合リファラルチャネルがあります。
* **デバイスタイプ** – 訪問者のデバイスタイプ（デスクトップ、モバイルまたはすべてのデバイス）によってトラフィックを分析します。
  **地域** – 様々な地域のリファラルパターンを表示します。

目的のフィルターを選択したら、「**フィルターを適用**」をクリックして、選択をダッシュボードに適用します。

## リファラルパフォーマンス全体 {#overall-performance}

ダッシュボードでは、次のような主要指標が表示され、リファラルの全体的なパフォーマンスがハイライト表示されます。

* **Total Referral Traffic** – すべてのソースからのリファラルトラフィックの合計。
* **LLM からのリファラルトラフィック** - LLM からのリファラルトラフィックの合計。
* **同意率** – 同意プロンプトを受け入れる訪問者の割合。
* **バウンス率** - エンゲージメントイベントがなかったリファラルソースのセッションの割合。

![&#x200B; リファラルページ &#x200B;](/help/dashboards/assets/referral-traffic.png)

上記の全体的なパフォーマンス指標に加えて、様々な市場、リファラルソース、ページインテントのカテゴリをまたいだトラフィック分布を示す 3 つの追加のパネル <!-- the **Top Regions** panel breaks down traffic by geography. Meanwhile, the **Top Referral Sources** panel shows the platforms driving the most visits. Trend indicators for the metrics show how these values are changing over time compared to the previous period.--> あります

<!--## Top Referral URLs {#top-referrals}

The Top Referral URLs list surfaces your site's most visited pages from referrals.

![Top Referral URLs](/help/dashboards/assets/top-url.png)-->

## リファラルソースの詳細と URL パフォーマンス分析 {#traffic-details}

リファラルソースの詳細および URL パフォーマンス分析テーブルは、トラフィック量と品質の両方を評価するのに役立ちます。 詳しくは、以下の各タブをクリックしてください。

![&#x200B; リファラルトラフィックの詳細 &#x200B;](/help/dashboards/assets/traffic-details.png)

>[!BEGINTABS]

>[!TAB  リファラルソースの詳細 ]

リファラルソースの詳細ビューでは、OpenAI、Microsoft、Google、Perplexity など、様々なプラットフォームからのトラフィックを分類します。 訪問数、バウンス率、チャネルタイプなどの主要指標が表示され、サイトに最も関与しているトラフィックを推進している AI および検索ソースを理解するのに役立ちます。

* **Source** – 紹介トラフィックのソース。
* **訪問回数** – 各ソースの訪問総数。
* **バウンス率** - エンゲージメントイベントがなかったリファラルソースからのセッションの割合。
* **チャネル** - ソースのチャネル（獲得、有料、またはその両方）。

>[!TAB URL パフォーマンス分析 ]

URL パフォーマンス分析ビューは、LLM およびその他のソースからのリファラルトラフィック量に基づいて、トップパフォーマンスのページをランク付けします。 トラフィック、バウンス率、同意率、ページインテントなどの指標がハイライト表示され、どのページが AI 駆動の参照から最も関心のある訪問者を引き付け、保持するかを特定するのに役立ちます。 このテーブルには、トピックにすばやくアクセスするための検索フィールドがあります。

>[!ENDTABS]

両方のテーブルで、「**エクスポート**」オプションを使用してテーブル .csv をダウンロードし、インサイトをチームで共有したり、エグゼクティブレポートにテーブルを含めたりできます。 さらに、両方のテーブルについて、「**列を設定**」ボタンをクリックして、表示する指標をカスタマイズできます。
