---
title: 関連する FAQ の追加
description: LLM Optimizerが、AI エージェント向けの構造化されたQ&A コンテンツを欠く、トラフィックの多いページをどのように特定するのか、そしてEdgeのOptimizeでAIが生成したFAQをレビューしてデプロイする方法をご紹介します。
feature: Opportunities
autotag-review: '2026-07-15T16:47:24.291Z'
TQID: 'https://experienceleague.adobe.com/ObmJKEvR9-ovzugCtAsRkcUBemcsMw6cNwizkuKYPcc'
product_v2:
  - id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2:
  - id: e1b649f0-0a61-46e4-9082-64d5cb2576c6
  - id: ef4e63f5-cb4d-462d-bf9a-1f617edf2a3a
subfeature_v2:
  - id: bbfc1b77-44c5-4fe8-b65f-ec160fe0d021
role_v2:
  - id: b69b2659-1057-424e-8fc5-ed9e016dc554
topic_v2:
  - id: cdd65e7e-8839-44a2-bc21-0e03623b5dd1
  - id: e1e0219c-f879-479f-8427-888ed2a6e9c2
source-git-commit: 2705cf26faea9c09817bbdcec4b4c531552df7ba
workflow-type: tm+mt
source-wordcount: 742
ht-degree: 10%

---


# 関連する FAQ の追加

「関連するFAQを追加」では、AI担当者が回答を生成する際に頻繁に利用する、構造化されたQ&amp;A コンテンツが欠如しているトラフィックの多いページを特定します。 既存のページ素材に基づいた、関連性の高い&#x200B;**インテントに沿ったFAQ** コンテンツが導入されます。 これにより、担当者はユーザーの質問とコンテンツをより直接一致させることができます。

影響を受けるURLごとに、AIが生成したFAQの提案を確認し、[Edgeで最適化](/help/dashboards/optimize-at-edge/overview.md)してデプロイすることで、Content Management System （CMS）の変更を必要とせずにAgentic Trafficがより明確なQ&amp;A コンテキストを受け取れるようになります。

## 課題の解決方法

修正は、[Edge](/help/dashboards/optimize-at-edge/overview.md)での最適化を使用して適用されます。次の内容を使用します。

- AI エージェントに事前レンダリング済みのHTML スナップショットを提供します。
- 取得したHTMLのFAQ コンテンツで、ページを充実させることができます。
- CDN レイヤーで機能します（CMSは変更されません）。
- AIのみ：人間の訪問者やSEO ボットには影響しません
- 数分でデプロイされ、LLM Optimizer インターフェイスから&#x200B;**完全に元に戻すことができます**。

## 仕組み

LLM Optimizerは、ブランドのプロンプトセットにもとづいて、Q&amp;A コンテンツが欠落している、または不足している、トラフィックの多いページを特定します。 影響を受けるURLは、**現在の推奨事項** タブの&#x200B;**URLと推奨事項** テーブルに表示されます。このタブでは、行を展開して各推奨事項を調べることができます。

現在の提案に関する提案が含まれる![URL、FAQ プロンプトとAIが生成した回答が含まれる行の拡張](/help/dashboards/opportunities/assets/add-relevant-faqs-expand.png)

候補&#x200B;**テーブルを含む** URLには、AIによる検出に役立つFAQが表示されます。 提案は、**現在の提案**、**修正提案**、**無視された提案**&#x200B;に整理されます。 各 URL について、次の操作を実行できます。

- **行**&#x200B;を展開して、そのページの提案されたFAQ コンテンツを表示します。
- エージェント型トラフィックの前後の比較を&#x200B;**プレビュー**&#x200B;します。
- **LLM Optimizer以外で商談に対処した場合は、**&#x200B;を修正済みとしてマークします。
- **無視**：関連しない候補に対して実行します。

展開された各項目には、FAQ **プロンプト**、**AIが生成した**&#x200B;の提案された回答、概要&#x200B;**推論**、ページに関連付けられた&#x200B;**ソース**&#x200B;が一覧表示されます。 また、この表には、優先順位付けに役立つFAQがURLと&#x200B;**Agentic traffic （4週間）**&#x200B;ごとに提案される数も示されています。

行の&#x200B;**プレビュー**&#x200B;をクリックして、最適化プレビューを開きます。 エージェント型トラフィックのページの現在の見た目と、最適化後のビュー（新しい&#x200B;**FAQ** ブロックなど）を比較します。

![最適化をプレビューして、現在のエージェントと最適化後のエージェントのビューをFAQと比較](/help/dashboards/opportunities/assets/add-relevant-faqs-ui-01.png)

行のチェックボックスを使用して、配送するFAQの提案を選択します。 フッターには、選択された数が表示され、**修正済みとしてマーク**、**推奨事項を無視**、**最適化をデプロイ**&#x200B;できます。

![&#x200B; デプロイの最適化に関する現在の提案に関するFAQ候補を選択](/help/dashboards/opportunities/assets/add-relevant-faqs-ui-02.png)

### 最適化のデプロイ

エッジで公開する準備ができたら、**最適化をデプロイ**&#x200B;をクリックします。 「**Edgeにデプロイ**」ダイアログには、プッシュするURL、質問、および回答が一覧表示されます。 リストを確認し、**デプロイ**&#x200B;または&#x200B;**キャンセル**&#x200B;のいずれかを選択します。

![Edge にデプロイダイアログ](/help/dashboards/opportunities/assets/add-relevant-faqs-ui-03.png)

デプロイが正常に完了すると、**デプロイメント完了**&#x200B;は、本番環境に公開された最適化の数を確認します。 ダイアログを閉じ、**修正候補**&#x200B;を開いてステータスを確認します。

![&#x200B; デプロイメント完了の確認](/help/dashboards/opportunities/assets/add-relevant-faqs-ui-04.png)

>[!NOTE]
>
>最適化をデプロイするには、Edge での最適化のオンボーディングプロセスを完了する必要があります。 まだオンボードしていない場合は、「**最適化をデプロイ**」をクリックすると、オンボーディングプロセスに移動します。 Edge での最適化の仕組み、サポートされている CDNプロバイダー、オンボーディングプロセスについて詳しくは、[Edge での最適化](/help/dashboards/optimize-at-edge/overview.md)ページを参照してください。

### 修正候補とライブ表示

**修正候補**&#x200B;で、デプロイされたURLは、ステータス列に&#x200B;**最適化**&#x200B;と表示されます。 ライブ FAQ コンテンツを確認するには、行を展開し、分析に&#x200B;**詳細**&#x200B;を使用するか、**ライブを表示**&#x200B;をクリックして、検証用に提供された&#x200B;**現在のページコンテンツ**&#x200B;の読み取り専用ビューを開きます（挿入された&#x200B;**FAQ** セクションを含む）。

![最適化されたステータス、ライブ表示、ロールバックを含む修正済みの提案](/help/dashboards/opportunities/assets/add-relevant-faqs-fixed.png)

「**ライブを表示**」ウィンドウには、そのチェックに表示されているページ構造とFAQ コピーが表示されます。

![&#x200B; ライブを表示 – FAQを含む現在のページコンテンツ &#x200B;](/help/dashboards/opportunities/assets/add-relevant-faqs-ui-05.png)

## ロールバック

変更を加えた場合は、デプロイした最適化をすべてロールバックできます。 **修正候補** ビューから、元に戻す最適化された行を選択し、ヘッダーの「**ロールバック**」をクリックできます。

**ロールバック** ダイアログには、ロールバックされる提案が一覧表示され、展開された最適化が元に戻されることを示す短い警告が表示されます。 リストを確認し、**ロールバック**&#x200B;または&#x200B;**キャンセル**&#x200B;をクリックします。

![&#x200B; ロールバックダイアログに、元に戻す候補が一覧表示されます](/help/dashboards/opportunities/assets/add-relevant-faqs-ui-07.png)

操作が完了すると、**正常にロールバックされました**&#x200B;の概要が表示されます。ダッシュボードに戻るには、この概要を閉じます。

![&#x200B; ロールバック完了 – ロールバックが完了しました](/help/dashboards/opportunities/assets/add-relevant-faqs-ui-08.png)

## デモで試す

[Frescopa デモ &#x200B;](https://play.llmo.now/org/demo-org)で、「関連するFAQを追加」ワークフローを確認します。
