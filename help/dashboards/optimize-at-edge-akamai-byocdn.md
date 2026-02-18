---
title: Edgeでの最適化 – Akamai （BYOCDN）
description: LLM OptimizerのEdgeで最適化をおこなうために Akamai BYOCDN を設定する方法を説明します。
feature: Opportunities
source-git-commit: 23752b30294c3d467ca477b085aa521cad0f72ca
workflow-type: tm+mt
source-wordcount: '302'
ht-degree: 26%

---


# Akamai （BYOCDN）

この設定により、エージェンティックトラフィック（AI ボットや LLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 人間の訪問者と SEO ボットは、通常どおりオリジンから提供され続けます。 設定をテストするには、セットアップが完了した後、応答でヘッダー `x-edgeoptimize-request-id` を探します。

**前提条件**

Akamai Property Manager ルールを設定する前に、次のことを確認してください。

* ドメインの Akamai Property Manager にアクセスします。
* LLM Optimizerのオンボーディングプロセスを完了しました。
* LLM Optimizerへの CDN ログ転送が完了しました。
* LLM Optimizer UI から取得されたEdge最適化 API キー。

{{retrieve-byocdn-api-key}}

**設定**

次の Akamai Property Manager ルールは、LLM ユーザーエージェントをEdge Optimize にルーティングします。 設定には、次の手順が含まれます。

**1. ルーティング条件を設定（ユーザーエージェントの一致）**

次の user-agents:image.png のルーティングを設定

```
 *AdobeEdgeOptimize-AI*,
 *ChatGPT-User*,
 *GPTBot*,
 *OAI-SearchBot*,
 *PerplexityBot*,
 *Perplexity-User*
```

![ルーティング条件を設定](/help/assets/optimize-at-edge/akamai-step1-routing.png)

**2. オリジンと SSL の動作を設定**

オリジンを `live.edgeoptimize.net` に設定し、SAN を `*.edgeoptimize.net` に一致させる

![オリジンと SSL の動作を設定](/help/assets/optimize-at-edge/akamai-step2-origin.png)

**3. キャッシュキー変数を設定**

キャッシュキー変数 `PMUSER_EDGE_OPTIMIZE_CACHE_KEY` を `LLMCLIENT=TRUE;X_FORWARDED_HOST={{builtin.AK_HOST}}` に設定します

![キャッシュキー変数を設定](/help/assets/optimize-at-edge/akamai-step3-cachekey.png)

**4. キャッシュルール**

![キャッシュルール](/help/assets/optimize-at-edge/akamai-step4-rules.png)

**5. 受信リクエストヘッダーを変更**

次の受信リクエストヘッダーを設定します。
LLMO から取得した API キーに `x-edgeoptimize-api-key` しています
`x-edgeoptimize-config` ～ `LLMCLIENT=TRUE;`
`x-edgeoptimize-url` ～ `{{builtin.AK_URL}}`

![受信リクエストヘッダーを変更](/help/assets/optimize-at-edge/akamai-step5-request.png)

**6. 受信応答ヘッダーを変更**

![受信応答ヘッダーを変更](/help/assets/optimize-at-edge/akamai-step6-response.png)

**7. キャッシュ ID の変更**

![キャッシュ ID の変更](/help/assets/optimize-at-edge/akamai-step7-cacheid.png)

**8. 送信リクエストヘッダーを変更**

ヘッダー `x-forwarded-host` `{{builtin.AK_HOST}}` に設定

![&#x200B; 送信リクエストヘッダーの変更 &#x200B;](/help/assets/optimize-at-edge/akamai-step8-outgoing-request.png)

**9. サイトフェイルオーバー**

![サイトフェイルオーバー](/help/assets/optimize-at-edge/akamai-step9-failover.png)

![フェイルオーバー動作](/help/assets/optimize-at-edge/akamai-step9-failover-behaviors.png)

![フェイルオーバールール](/help/assets/optimize-at-edge/akamai-step9-failover-rules.png)

サイトのフェイルオーバーにより、Edge Optimize から `4XX` または `5XX` エラーが返された場合に、リクエストがデフォルトのオリジンに自動的にルーティングされ、エンドユーザーは引き続きレスポンスを受け取ることができます。

| シナリオ | 動作 |
| --- | --- |
| Edge最適化による `2XX` の返り値 | 最適化された応答がクライアントに提供されます。 |
| Edge Optimize は、`4XX` または `5XX` を返します。 | リクエストはデフォルトのオリジンにルーティングされます。 |

{{verify-setup-byocdn}}

{{return-to-overview}}
