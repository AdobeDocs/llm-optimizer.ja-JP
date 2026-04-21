---
title: Edgeで最適化 – Fastly （BYOCDN）
description: LLM OptimizerのEdgeでFastly BYOCDN for Optimizeを設定する方法について説明します。
feature: Opportunities
source-git-commit: 13d2f4bbd1f9d3886f89f80df0e76093f2afdf13
workflow-type: tm+mt
source-wordcount: '348'
ht-degree: 6%

---


# Fastly （BYOCDN）

この設定により、エージェント型トラフィック（AI ボットおよびLLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 通常どおり、人間の訪問者とSEO ボットは、元のページから引き続き提供されます。 設定をテストするには、設定が完了した後、応答でヘッダー`x-edgeoptimize-request-id`を探します。

**前提条件**

Fastly VCL ルールを設定する前に、次のことを確認してください。

* ドメイン用にFastlyにアクセスします。
* LLM Optimizer UIから取得したEdge Optimize API キー。 手順については、[API キーの取得](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#production-api-key)を参照してください。
* （オプション）ステージング ルーティングをテストするには、[&#x200B; ステージング API キー](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#staging-api-key-optional)を参照してください。

**設定**

次の3つのVCL スニペットをFastly サービスに追加します。 これらのスニペットは、Edge Optimizeへのエージェント型リクエストのルーティング、キャッシュキーの分離、デフォルトのオリジンへのフェイルオーバーを処理します。

![Fastly VCL](/help/assets/optimize-at-edge/fastly-vcl.png)

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
  set req.backend = F_Default_Origin;
  restart;
}

if (!req.http.x-edgeoptimize-config && req.http.x-edgeoptimize-request == "failover") {
  set resp.http.x-edgeoptimize-fo = "1";
}
```

**フェールオーバー**

`vcl_deliver` スニペットは、フェールオーバーを自動的に処理します。 Edge Optimizeが`4XX`または`5XX` エラーを返した場合、リクエストは再起動され、デフォルトのオリジンにルーティングされ、エンドユーザーは引き続き応答を受け取ります。 フェールオーバー応答には、`x-edgeoptimize-fo: 1` ヘッダーが含まれます。

| シナリオ | 動作 |
| --- | --- |
| Edge Optimizeは`2XX`を返します | 最適化された応答がクライアントに提供されます。 |
| Edge Optimizeは`4XX`または`5XX`を返します | リクエストは再起動され、デフォルトのオリジンから提供されます。 |
| フェールオーバー応答 | ヘッダー`x-edgeoptimize-fo: 1`が含まれています。 |

**ファイアウォール ルールを使用してEdgeで最適化を許可する（オプション）**

{{waf-allowlist-setup}}

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

{{verify-routing-status-in-ui}}

{{return-to-overview}}
