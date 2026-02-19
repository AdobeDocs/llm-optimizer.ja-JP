---
title: Edgeでの最適化 – Akamai （BYOCDN）
description: LLM OptimizerのEdgeで最適化をおこなうために Akamai BYOCDN を設定する方法を説明します。
feature: Opportunities
source-git-commit: 9230e525340bb951fcd9f2ae1f88bad557d5b7d7
workflow-type: tm+mt
source-wordcount: '587'
ht-degree: 14%

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

サイト フェールオーバー構成には、フェールオーバー動作（optimize-at-edge ルーティングのメイン ルール内で構成）と、個別のフェールオーバーテスト ヘッダー規則の 2 つの部分があります。

**9a。 サイト フェールオーバー動作（最適化エッジ ルーティングのメイン ルール内）**

メインのルーティングルール内で、次のようにサイトのフェイルオーバー動作と高度な XML スニペットを設定します。

![サイトフェイルオーバー](/help/assets/optimize-at-edge/akamai-step9-failover.png)

高度な XML を使用して、値 `fo` のリクエストヘッダー `x-edgeoptimize-request` を追加します。

```
<forward:availability.fail-action2>
<add-header>
<status>on</status>
<name>x-edgeoptimize-request</name>
<value>fo</value>
</add-header>
</forward:availability.fail-action2>
```

![フェイルオーバー動作](/help/assets/optimize-at-edge/akamai-step9-failover-behaviors.png)

**9b。 フェールオーバーテスト ヘッダーの規則（兄弟の規則）**

>[!IMPORTANT]
>
>**EdgeOptimize Failover - Test Header** ルールを、その内部にネストされた **ではない** ルーティングルールの **兄弟** として（同じレベルで）作成します。 Akamai Property Manager のルールツリーで、階層は次のようになります。
>
>```
>▼ Parent Rule
>   ▶ Optimize at Edge Routing     ← routing rule
>       EdgeOptimize Failover - Test Header       ← sibling, same level
>```
>
>これにより、フェールオーバーテスト ヘッダー規則が、1 つだけでなく **すべて** のルーティング規則に対して評価されるようになります。

リクエストヘッダー `x-edgeoptimize-request` の値が `fo` の場合は、送信応答ヘッダー `x-edgeoptimize-fo` を `true` に設定します。

![フェイルオーバールール](/help/assets/optimize-at-edge/akamai-step9-failover-rules.png)

サイトのフェイルオーバーにより、Edge Optimize から `4XX` または `5XX` エラーが返された場合に、リクエストがデフォルトのオリジンに自動的にルーティングされ、エンドユーザーは引き続きレスポンスを受け取ることができます。

| シナリオ | 動作 |
| --- | --- |
| Edge最適化による `2XX` の返り値 | 最適化された応答がクライアントに提供されます。 |
| Edge Optimize は、`4XX` または `5XX` を返します。 | リクエストはデフォルトのオリジンにルーティングされます。 |

**設定の確認**

設定が完了したら、ボットトラフィックがEdge Optimize にルーティングされていることと、人間のトラフィックが影響を受けないことを確認します。

**1. ボットトラフィックのテスト （最適化する必要があります）**

エージェント user-agent を使用して AI ボットリクエストをシミュレートします。

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: chatgpt-user"
```

リクエストが成功した応答には `x-edgeoptimize-request-id` ヘッダーが含まれ、リクエストがEdge Optimize を通じてルーティングされたことを確認します。

```
< HTTP/2 200
< x-edgeoptimize-request-id: 50fce12d-0519-4fc6-af78-d928785c1b85
```

**2. 人間によるトラフィックのテスト（影響を受けない）**

通常の人間によるブラウザーリクエストをシミュレートする：

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36"
```

応答には、`x-edgeoptimize-request-id` ヘッダーを含める **しない** でください。 Edgeで最適化を有効にする前と同じページコンテンツおよび応答時間である必要があります。

**3. 2 つのシナリオの違い**

| ヘッダー | ボットトラフィック（最適化） | ヒューマントラフィック（影響を受けない） |
|---|---|---|
| `x-edgeoptimize-request-id` | 現在 – 一意のリクエスト ID が含まれます | 不在 |
| `x-edgeoptimize-fo` | フェールオーバーが発生した場合にのみ表示されます（値：`1`） | 不在 |

トラフィックルーティングのステータスは、LLM Optimizer UI でも確認できます。 **顧客設定** に移動し、「**CDN 設定**」タブを選択します。

![&#x200B; ルーティングを有効にした AI トラフィックルーティングステータス &#x200B;](/help/assets/optimize-at-edge/byocdn-CDN-traffic-routed-tick.png)

{{return-to-overview}}
