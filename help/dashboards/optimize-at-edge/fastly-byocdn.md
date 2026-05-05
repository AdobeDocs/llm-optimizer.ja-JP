---
title: Edge での最適化 - Fastly（BYOCDN）
description: LLM Optimizer の Edge での最適化を行うのに Fastly BYOCDN を設定する方法について説明します。
feature: Opportunities
source-git-commit: 0c5ab87db0856dcce4be4ab34b9725b9bef788dd
workflow-type: tm+mt
source-wordcount: '349'
ht-degree: 92%

---


# Fastly（BYOCDN）

この設定では、エージェントトラフィック（AI ボットおよび LLM ユーザーエージェントからのリクエスト）を Edge での最適化バックエンドサービス（`live.edgeoptimize.net`）にルーティングします。 人間の訪問者と SEO ボットは、通常どおりオリジンから引き続き提供されます。 設定をテストするには、設定が完了したら、応答のヘッダー `x-edgeoptimize-request-id` を探します。

**前提条件**

Fastly VCL ルールを設定する前に、以下を確認します。

* ドメインの Fastly へのアクセス権。
* LLM Optimizer UI から取得された Edge Optimize API キー。 手順については、[API キーの取得](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#production-api-key)を参照してください。
* （オプション）ステージング ルーティングをテストするには、[&#x200B; ステージング API キー](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#staging-api-key-optional)を参照してください。

**設定**

Fastly サービスに次の 3 つの VCL スニペットを追加します。 これらのスニペットは、エージェント型リクエストを Edge での最適化にルーティングする処理、キャッシュキーの分離、デフォルトのオリジンへのフェイルオーバーを処理します。

![Fastly バックエンド設定](/help/assets/optimize-at-edge/fastly-backend-config.png)

![VCL スニペットを追加](/help/assets/optimize-at-edge/add-vcl-snippets.png)

**vcl_recv スニペット**

```
unset req.http.x-edgeoptimize-url;
unset req.http.x-edgeoptimize-config;
unset req.http.x-edgeoptimize-api-key;
unset req.http.x-edgeoptimize-fetcher-key; # Optional (required only in case of WAF)

if (!req.http.x-edgeoptimize-request
    && req.http.user-agent ~ "(?i)(AdobeEdgeOptimize-AI|ChatGPT-User|GPTBot|OAI-SearchBot|PerplexityBot|Perplexity-User)") {
  set req.http.x-forwarded-host = req.http.host; # required for identifying the original host
  set req.http.x-edgeoptimize-url = req.url; # required for identifying the original url
  set req.http.x-edgeoptimize-config = "LLMCLIENT=TRUE;"; # required for cache key
  set req.http.x-edgeoptimize-api-key = "<YOUR API KEY>"; # required for identifying the client
  set req.http.x-edgeoptimize-fetcher-key = "<YOUR FETCHER KEY>"; # Optional (required only in case of WAF)
  set req.backend = F_EDGE_OPTIMIZE;
  return(lookup);
}
```

**vcl_hash スニペット**

```
if (req.http.x-edgeoptimize-config) {
  set req.hash += "edge-optimize";
  set req.hash += req.http.x-edgeoptimize-config;
}
```

**vcl_deliver スニペット**

```
if (req.http.x-edgeoptimize-config && resp.status >= 400) {
  set req.http.x-edgeoptimize-request = "failover";
  restart;
}

if (req.http.x-edgeoptimize-config) {
  return(deliver);
}

if (!req.http.x-edgeoptimize-config && req.http.x-edgeoptimize-request == "failover") {
  set resp.http.x-edgeoptimize-fo = "1";
}
```

**フェイルオーバー**

`vcl_deliver` スニペットは、フェイルオーバーを自動的に処理します。 Edge での最適化が `4XX` または `5XX` エラーを返した場合、リクエストは再開され、デフォルトのオリジンにルーティングされるので、エンドユーザーは応答を引き続き受信できます。 フェイルオーバー応答には、`x-edgeoptimize-fo: 1` ヘッダーが含まれます。

| シナリオ | 動作 |
| --- | --- |
| Edge での最適化で `2XX` が返される | 最適化された応答がクライアントに提供されます。 |
| Edge での最適化で `4XX` または `5XX` が返される | リクエストが再開され、デフォルトのオリジンから提供されます。 |
| フェイルオーバー応答 | ヘッダー `x-edgeoptimize-fo: 1` を含みます。 |

**ファイアウォール ルールを使用してEdgeで最適化を許可する（オプション）**

{{waf-allowlist-setup}}

**設定の検証**

設定が完了したら、ボットトラフィックが Edge での最適化にルーティングされていることと、人間のトラフィックに影響がないことを確認します。

**1. ボットトラフィックをテスト（最適化する必要があります）**

エージェント型ユーザーエージェントを使用して、AI ボットリクエストをシミュレートします。

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: chatgpt-user"
```

正常な応答には、リクエストが Edge での最適化を経由してルーティングされたことを確認する `x-edgeoptimize-request-id` ヘッダーが含まれます。

```
< HTTP/2 200
< x-edgeoptimize-request-id: 50fce12d-0519-4fc6-af78-d928785c1b85
```

**2. 人間のトラフィックをテスト（影響を受けません）**

通常の人間によるブラウザーリクエストをシミュレートします。

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36"
```

応答には、`x-edgeoptimize-request-id` ヘッダーを含め&#x200B;**ない**&#x200B;でください。 ページのコンテンツと応答時間は、Edge での最適化を有効にする前と同じ状態を維持する必要があります。

**3. 2 つのシナリオを区別する方法**

| ヘッダー | ボットトラフィック（最適化） | 人間のトラフィック（影響を受けない） |
|---|---|---|
| `x-edgeoptimize-request-id` | 存在 - 一意のリクエスト ID が含まれます | 不在 |
| `x-edgeoptimize-fo` | フェイルオーバーが発生した場合のみ存在（値：`1`） | 不在 |

{{verify-routing-status-in-ui}}

{{return-to-overview}}
