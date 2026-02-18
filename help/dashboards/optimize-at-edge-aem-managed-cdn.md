---
title: Edgeでの最適化 – AEM Cloud Service の管理による CDN （Fastly）
description: LLM OptimizerのEdgeで最適化をおこなうために、AEM Cloud Service Managed CDN （Fastly）を設定する方法について説明します。
feature: Opportunities
source-git-commit: 8cdd15413555057165f69ea4d5a15b243ab9098d
workflow-type: tm+mt
source-wordcount: '329'
ht-degree: 16%

---


# AEM Cloud Service の管理による CDN （Fastly）

この設定により、エージェンティックトラフィック（AI ボットや LLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 人間の訪問者と SEO ボットは、通常どおりオリジンから提供され続けます。 設定をテストするには、セットアップが完了した後、応答でヘッダー `x-edgeoptimize-request-id` を探します。

**前提条件**

エージェンティックトラフィックのEdgeへのルーティングを開始するには最適化：

1. **顧客設定** に移動し、「**CDN 設定**」タブを選択します。

   ![&#x200B; 顧客設定に移動 &#x200B;](/help/assets/optimize-at-edge/prereq-customer-config-nav.png)

2. 「**最適化をデプロイするための AI トラフィックルーティング**」で、「**最適化を AI エージェントにデプロイ**」チェックボックスをオンにします。 Adobe チームが、お客様に代わってルーティング設定を処理します。

   ![AI エージェントへのティックのデプロイの最適化 &#x200B;](/help/assets/optimize-at-edge/prereq-deploy-checkbox.png)

3. チェックボックスを有効にすると、ステータスにセットアップが進行中であることが示されます。 Adobe チームがルーティング設定を行います。

   ![AI トラフィックルーティングの設定中 &#x200B;](/help/assets/optimize-at-edge/prereq-traffic-routing-progress.png)

   ルーティングが設定され、アクティブになると、ステータスが更新され、ルーティングが正常に有効になったことを示す緑色のチェックマークが表示されます。 ユーザー側でこれ以上のアクションを行う必要はありません。

さらに、上記の手順に関するヘルプが必要な場合は、Adobe アカウントチームまたは `llmo-at-edge@adobe.com` に問い合わせてください。

**Cloud Manager パイプラインを介したセルフサービスルーティング**

Cloud Manager パイプラインを使用して自分でルーティングを設定する場合は、次の手順に従います。 ルーティング設定は、[originSelector CDN ルール](https://experienceleague.adobe.com/ja/docs/experience-manager-cloud-service/content/implementing/content-delivery/cdn-configuring-traffic#origin-selectors)を使用して行われます。 前提条件は、次のとおりです。

* ルーティングするドメインを決定します。
* ルーティングするパスを決定します。
* ルーティングするユーザーエージェントを決定します（推奨される正規表現）。

ルールをデプロイするには、次の操作を実行する必要があります。

* [&#x200B; 設定パイプライン &#x200B;](https://experienceleague.adobe.com/ja/docs/experience-manager-cloud-service/content/operations/config-pipeline) を作成します。
* リポジトリの `cdn.yaml` 設定ファイルをコミットします。
* 設定パイプラインを実行します。

```
kind: "CDN"
version: "1"
data:
  # Origin selectors to route to Edge Optimize backend
  originSelectors:
    rules:
      - name: route-to-edge-optimize-backend
        when:
          allOf:
            - reqHeader: x-edgeoptimize-request
              exists: false # avoid loops when requests comes from Edge Optimize
            - reqHeader: user-agent
              matches: "(?i)(AdobeEdgeOptimize-AI|ChatGPT-User|GPTBot|OAI-SearchBot|PerplexityBot|Perplexity-User)" # routed user agents
            - reqProperty: domain
              equals: "example.com" # routed domain
            - reqProperty: originalPath
              matches: '(/[^./]+|\.html|/)$' # routed extensions, with .html extension or without extension
            - anyOf:
              - { reqProperty: originalPath, in: [ "/page.html" ] } # routed pages, exact path matching
              - { reqProperty: originalPath, like: "/dir/*" } # routed pages, wildcard path matching
        action:
          type: selectOrigin
          originName: edge-optimize-backend
    origins:
      - name: edge-optimize-backend
        domain: "live.edgeoptimize.net"
```

{{verify-setup-adobe-aem-cs-cdn}}

{{return-to-overview}}
