---
title: Edgeで最適化 – Fastly （BYOCDN）
description: LLM OptimizerのEdgeで最適化をおこなうために Fastly BYOCDN を設定する方法を説明します。
feature: Opportunities
source-git-commit: 8cdd15413555057165f69ea4d5a15b243ab9098d
workflow-type: tm+mt
source-wordcount: '217'
ht-degree: 6%

---


# Fastly （BYOCDN）

この設定により、エージェンティックトラフィック（AI ボットや LLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 人間の訪問者と SEO ボットは、通常どおりオリジンから提供され続けます。 設定をテストするには、セットアップが完了した後、応答でヘッダー `x-edgeoptimize-request-id` を探します。

**前提条件**

Fastly VCL ルールを設定する前に、以下が揃っていることを確認します。

* お使いのドメインの Fastly へのアクセス。
* LLM Optimizerのオンボーディングプロセスを完了しました。
* LLM Optimizerへの CDN ログ転送が完了しました。
* LLM Optimizer UI から取得されたEdge最適化 API キー。

{{retrieve-byocdn-api-key}}

**設定**

次の 3 つの VCL スニペットを Fastly サービスに追加します。 これらのスニペットは、Edge Optimize へのルーティング元リクエスト、キャッシュキーの分離、デフォルトオリジンへのフェイルオーバーを処理します。

![Fastly VCL](/help/assets/optimize-at-edge/fastly-vcl.png)

![VCL スニペットを追加](/help/assets/optimize-at-edge/add-vcl-snippets.png)

**vcl_recv スニペット**

```
unset req.http.x-edgeoptimize-url;
unset req.http.x-edgeoptimize-config;
unset req.http.x-edgeoptimize-api-key;

if (!req.http.x-edgeoptimize-request
    && req.http.user-agent ~ "(?i)(AdobeEdgeOptimize-AI|ChatGPT-User|GPTBot|OAI-SearchBot|PerplexityBot|Perplexity-User)") {
  set req.http.x-forwarded-host = req.http.host; # required for identifying the original host
  set req.http.x-edgeoptimize-url = req.url; # required for identifying the original url
  set req.http.x-edgeoptimize-config = "LLMCLIENT=TRUE;"; # required for cache key
  set req.http.x-edgeoptimize-api-key = "<YOUR API KEY>"; # required for identifying the client
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

**フェイルオーバー**

`vcl_deliver` スニペットは、フェイルオーバーを自動的に処理します。 Edge Optimize が `4XX` または `5XX` エラーを返した場合、リクエストは再起動され、デフォルトのオリジンにルーティングされ戻されるので、エンドユーザーは引き続きレスポンスを受け取ることができます。 フェイルオーバー応答には、`x-edgeoptimize-fo: 1` ヘッダーが含まれます。

| シナリオ | 動作 |
| --- | --- |
| Edge最適化による `2XX` の返り値 | 最適化された応答がクライアントに提供されます。 |
| Edge Optimize は、`4XX` または `5XX` を返します。 | リクエストが再開され、デフォルトの接触チャネルから提供されます。 |
| フェイルオーバー応答 | ヘッダー `x-edgeoptimize-fo: 1` を含みます。 |

{{verify-setup-byocdn}}

{{return-to-overview}}
