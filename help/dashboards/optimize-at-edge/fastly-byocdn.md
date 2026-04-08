---
title: Edgeで最適化 – Fastly （BYOCDN）
description: LLM OptimizerのEdgeでFastly BYOCDN for Optimizeを設定する方法について説明します。
feature: Opportunities
source-git-commit: da789100d814004687de2f46e18a295671dec4b8
workflow-type: tm+mt
source-wordcount: '407'
ht-degree: 5%

---


# Fastly （BYOCDN）

この設定により、エージェント型トラフィック（AI ボットおよびLLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 通常どおり、人間の訪問者とSEO ボットは、元のページから引き続き提供されます。 設定をテストするには、設定が完了した後、応答でヘッダー`x-edgeoptimize-request-id`を探します。

**前提条件**

Fastly VCL ルールを設定する前に、次のことを確認してください。

* ドメイン用にFastlyにアクセスします。
* LLM Optimizer オンボーディングプロセスを完了しました。
* LLM OptimizerへのCDN ログの転送が完了しました。
* LLM Optimizer UIから取得したEdge Optimize API キー。
* （オプション）最初にステージングホスト名でルーティングをテストする場合は、ステージング Edge Optimize API キー。

{{retrieve-byocdn-api-key}}

{{retrieve-staging-edge-optimize-api-key}}

**設定**

次の3つのVCL スニペットをFastly サービスに追加します。 これらのスニペットは、Edge Optimizeへのエージェント型リクエストのルーティング、キャッシュキーの分離、デフォルトのオリジンへのフェイルオーバーを処理します。

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

**フェールオーバー**

`vcl_deliver` スニペットは、フェールオーバーを自動的に処理します。 Edge Optimizeが`4XX`または`5XX` エラーを返した場合、リクエストは再起動され、デフォルトのオリジンにルーティングされ、エンドユーザーは引き続き応答を受け取ります。 フェールオーバー応答には、`x-edgeoptimize-fo: 1` ヘッダーが含まれます。

| シナリオ | 動作 |
| --- | --- |
| Edge Optimizeは`2XX`を返します | 最適化された応答がクライアントに提供されます。 |
| Edge Optimizeは`4XX`または`5XX`を返します | リクエストは再起動され、デフォルトのオリジンから提供されます。 |
| フェールオーバー応答 | ヘッダー`x-edgeoptimize-fo: 1`が含まれています。 |

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

**4. ステージング ドメイン （オプション）**

LLM Optimizerのステージングホスト名とステージング API キーを使用する場合は、**ステージング** API キーを使用して、同じVCL スニペットを&#x200B;**ステージング** Fastly サービスに追加します。 次に、ステージングホストのボットトラフィックを確認します。

```
curl -svo /dev/null https://staging.example.com/page.html \
  --header "user-agent: chatgpt-user"
```

`https://staging.example.com/page.html`を実際のステージング URLとパスに置き換えます。 応答が成功すると、`x-edgeoptimize-request-id` ヘッダーが含まれます。

{{verify-routing-status-in-ui}}

{{return-to-overview}}
