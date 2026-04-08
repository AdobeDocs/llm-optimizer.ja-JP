---
title: Edgeでの最適化 – AEM Cloud Service Managed CDN （Fastly）
description: LLM OptimizerのEdgeで、AEM Cloud Service Managed CDN （Fastly） for Optimizeを設定する方法について説明します。
feature: Opportunities
source-git-commit: 0c7ccadbb40c8c119cb2a57cf8118708c33c4236
workflow-type: tm+mt
source-wordcount: '481'
ht-degree: 12%

---


# AEM Cloud Service Managed CDN （Fastly）

この設定により、エージェント型トラフィック（AI ボットおよびLLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 通常どおり、人間の訪問者とSEO ボットは、元のページから引き続き提供されます。 設定をテストするには、設定が完了した後、応答でヘッダー`x-edgeoptimize-request-id`を探します。

**前提条件**

エージェント型トラフィックのEdge Optimizeへのルーティングを開始するには：

1. LLM Optimizerで、**顧客設定**&#x200B;を開き、**CDN設定** タブを選択します。

   ![顧客設定に移動](/help/assets/optimize-at-edge/prereq-customer-config-nav.png)

2. 「**AI エージェントに最適化をデプロイ**」セクションを見つけます。 「**最適化エンジンを有効にする**」チェックボックスをオンにします。

   ![AI エージェントへの最適化のデプロイ – 保留中](/help/assets/optimize-at-edge/byocdn-deploy-optimizations-pending.png)

3. 確認ダイアログで、**有効**&#x200B;を選択します。 Adobe チームが、お客様の代わりにルーティング設定を処理します。

   ![最適化エンジン確認ダイアログを有効にする](/help/assets/optimize-at-edge/byocdn-enable-optimization-engine-dialog.png)

   ルーティングが設定され、アクティブになると、ルーティングが有効であることを確認する緑色のチェックマークが付いて、ステータスが&#x200B;**完了**&#x200B;に更新されます。 ユーザー側でこれ以上の操作は必要ありません。

   ![AI エージェントへの最適化のデプロイ – 完了](/help/assets/optimize-at-edge/byocdn-CDN-traffic-routed-tick.png)

上記の手順についてサポートが必要な場合は、Adobe アカウントチームまたは`llmo-at-edge@adobe.com`にお問い合わせください。

**Cloud Manager パイプライン経由のセルフサービスルーティング**

Cloud Manager パイプラインを使用してルーティングを自分で設定する場合は、次の手順に従います。 ルーティング設定は、[originSelector CDN ルール](https://experienceleague.adobe.com/ja/docs/experience-manager-cloud-service/content/implementing/content-delivery/cdn-configuring-traffic#origin-selectors)を使用して行われます。 前提条件は、次のとおりです。

* ルーティングするドメインを決定します。
* ルーティングするパスを決定します。
* ルーティングするユーザーエージェントを決定します（推奨される正規表現）。

ルールをデプロイするには、次の操作を実行する必要があります。

* [設定パイプライン ](https://experienceleague.adobe.com/ja/docs/experience-manager-cloud-service/content/operations/config-pipeline)を作成します。
* リポジトリ内の`cdn.yaml`設定ファイルをコミットします。
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

**設定を確認**

設定が完了したら、ボットトラフィックがEdge Optimizeにルーティングされ、人間のトラフィックが影響を受けないことを確認します。

**1. ボットトラフィックのテスト （最適化する必要があります）**

エージェント型ユーザーエージェントを使用してAI ボットリクエストをシミュレートします。

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: chatgpt-user"
```

応答が成功すると、`x-edgeoptimize-request-id` ヘッダーが含まれ、リクエストがEdge Optimizeを通じてルーティングされたことが確認されます。

```
< HTTP/2 200
< x-edgeoptimize-request-id: 50fce12d-0519-4fc6-af78-d928785c1b85
```

**2. 人間のトラフィックをテストします（影響を受けません）**

通常のヒューマンブラウザーリクエストをシミュレートします。

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36"
```

応答には、**not**&#x200B;に`x-edgeoptimize-request-id` ヘッダーを含める必要があります。 Edgeで最適化を有効にする前に、ページの内容と応答時間を同じにしておく必要があります。

**3. 2つのシナリオを区別する方法**

| ヘッダー | ボットトラフィック（最適化） | 人的トラフィック（影響なし） |
|---|---|---|
| `x-edgeoptimize-request-id` | 現在 – 一意のリクエスト IDを含む | 不在 |
| `x-edgeoptimize-fo` | フェールオーバーが発生した場合にのみ存在します（値：`1`） | 不在 |

**4. LLM Optimizer**&#x200B;のルーティング状況を確認する

LLM Optimizer UIでルーティングを確認することもできます。 **顧客設定**&#x200B;を開き、**CDN設定** タブを選択します。 ルーティングがアクティブな場合、「**AI エージェントに最適化をデプロイ**」セクションに「**完了**」と表示されます。

![AI エージェントへの最適化のデプロイ – 完了](/help/assets/optimize-at-edge/byocdn-CDN-traffic-routed-tick.png)

{{return-to-overview}}
