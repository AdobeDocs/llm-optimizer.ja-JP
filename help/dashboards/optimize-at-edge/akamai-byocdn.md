---
title: Edge での最適化 - Akamai（BYOCDN）
description: LLM Optimizer の Edge での最適化に Akamai BYOCDN を設定する方法について説明します。
feature: Opportunities
autotag-review: '2026-07-15T17:40:02.356Z'
TQID: 'https://experienceleague.adobe.com/XlHpXbtxqPl-XQQKWeQc3rbsizCT7U0TF1bQkyv0iM8'
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: d1956731-2adb-4bb7-8301-2b239254ac72id: e1b649f0-0a61-46e4-9082-64d5cb2576c6id: ef4e63f5-cb4d-462d-bf9a-1f617edf2a3aid: e0828736-236a-487b-a478-5a635455eadc
subfeature_v2: id: d23587d6-14d6-4e3f-9ee1-cc18623832e1id: e06fae5f-830b-4222-a469-b5e148d36465
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 9d2324e23e07f01e16c4fc16c96213d03214918f
workflow-type: tm+mt
source-wordcount: 795
ht-degree: 76%

---


# Akamai（BYOCDN）

この設定では、エージェントトラフィック（AI ボットおよび LLM ユーザーエージェントからのリクエスト）を Edge での最適化バックエンドサービス（`live.edgeoptimize.net`）にルーティングします。 人間の訪問者と SEO ボットは、通常どおりオリジンから引き続き提供されます。 設定をテストするには、設定が完了したら、応答のヘッダー `x-edgeoptimize-request-id` を探します。

**前提条件**

Akamai プロパティマネージャールールを設定する前に、次のことを確認してください。

* ドメインの Akamai Property Manager へのアクセス権。
* LLM Optimizer UI から取得された Edge Optimize API キー。 手順について詳しくは、[API キーの取得](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#production-api-key)を参照してください。
* （オプション）ステージングルーティングをテストするには、[Staging API キー](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#staging-api-key-optional)を参照してください。

**設定**

次の Akamai Property Manager ルールでは、エージェント型 HTML ページトラフィックを Edge での最適化にルーティングします。 設定には、次の手順が含まれます。

**1. ルーティング条件を設定（ユーザーエージェントと HTML トラフィックの一致）**

次のユーザーエージェントのルーティングを設定します。

```
 *AdobeEdgeOptimize-AI*
 *ChatGPT-User*
 *GPTBot*
 *OAI-SearchBot*
 *PerplexityBot*
 *Perplexity-User*
 *ClaudeBot*
 *Claude-User*
 *Claude-SearchBot*
```

>[!NOTE]
>
>Edge での最適化ルーティングルールは、エージェント型 HTML ページトラフィックにのみ適用します。 一般的な設定としては、リクエスト側の条件として、`html` に一致する&#x200B;**ファイル拡張子**&#x200B;や、拡張子のないページ URL に一致する `EMPTY_STRING` などを使用することがあります。 サイトが他の URL パターンから HTML を配信する場合や、API エンドポイントなどの拡張子のない非ページルートが含まれる場合は、パスベースの追加条件でルールを絞り込みます。

![ルーティング条件を設定](/help/assets/optimize-at-edge/akamai-step1-routing.png)

**2. オリジンと SSL の動作を設定**

オリジンを `live.edgeoptimize.net` として設定し、SAN を `*.edgeoptimize.net` に一致させます。

>[!NOTE]
>
>Edge での最適化ルールを追加した後にプロパティのアクティブ化が失敗する場合は、そのルールがデフォルトのルールとは異なるオリジンサーバーの SSL 検証モードを使用しているかどうかを確認します。 異なるモードを使用している場合は、Edge での最適化ルールをデフォルトのルールと一致するように更新します。 例えば、デフォルトのルールで&#x200B;**プラットフォーム設定**&#x200B;を使用している場合は、ここでも&#x200B;**プラットフォーム設定**&#x200B;を使用します。 必要な設定を使用できない場合は、Akamai サポートにお問い合わせください。

![オリジンと SSL の動作を設定](/help/assets/optimize-at-edge/akamai-step2-origin.png)

**3. キャッシュキー変数を設定**

キャッシュキー変数 `PMUSER_EDGE_OPTIMIZE_CACHE_KEY` を `LLMCLIENT=TRUE;X_FORWARDED_HOST={{builtin.AK_HOST}}` に設定します。

![キャッシュキー変数を設定](/help/assets/optimize-at-edge/akamai-step3-cachekey.png)

**4. キャッシュルール**

![キャッシュルール](/help/assets/optimize-at-edge/akamai-step4-rules.png)

**5. 受信リクエストヘッダーを変更**

次の受信リクエストヘッダーを設定します。
`x-edgeoptimize-api-key` を LLMO から取得した API キーに
`x-edgeoptimize-config` コピー先： `LLMCLIENT=TRUE;`
`x-edgeoptimize-url` を `{{builtin.AK_URL}}` に

![受信リクエストヘッダーを変更](/help/assets/optimize-at-edge/akamai-step5-request.png)

**ファイアウォールルールを通じて Edge での最適化を許可（オプション）**

{{waf-allowlist-setup}}

![プロパティマネージャーで x-edgeoptimize-fetcher-key ヘッダーを設定](/help/assets/optimize-at-edge/akamai-step10-fetcher-key.png)

>[!NOTE]
>
>また、Akamai Bot Manager で `*AdobeEdgeOptimize/1.0*` ユーザーエージェントと `x-edgeoptimize-fetcher-key` ヘッダーを許可リストに追加します。

**6. 受信応答ヘッダーを変更**

![受信応答ヘッダーを変更](/help/assets/optimize-at-edge/akamai-step6-response.png)

**7. キャッシュ ID の変更**

![キャッシュ ID の変更](/help/assets/optimize-at-edge/akamai-step7-cacheid.png)

**8. 送信リクエストヘッダーを変更**

`x-forwarded-host` ヘッダーを `{{builtin.AK_HOST}}` に設定します。

![送信リクエストヘッダーを変更](/help/assets/optimize-at-edge/akamai-step8-outgoing-request.png)

**9. サイトフェイルオーバー**

サイト フェールオーバー設定には、メインのOptimize at Edge ルーティング ルール内のフェールオーバー動作と、フォールバックが発生したときにレスポンス ヘッダーを追加する兄弟ルールの2つの部分があります。

**9a. サイト フェールオーバー動作の設定**

Edgeのメインの最適化ルーティングルール内で、**Site Failover Behavior**&#x200B;という名前の子ルールを作成します。 **Match Any**&#x200B;に設定し、次の条件を追加します。

* **応答ステータスコード**&#x200B;は`400` ～ `599`の範囲にあります。
* **オリジン タイムアウト**&#x200B;は`Yes`です。

![サイトフェイルオーバー](/help/assets/optimize-at-edge/akamai-step9-failover.png)

![ サイト フェールオーバー動作の設定](/help/assets/optimize-at-edge/akamai-step9-failover-settings.png)

**9b. フェールオーバー応答ヘッダー規則**&#x200B;を設定します

>[!IMPORTANT]
>
>**EdgeOptimize フェイルオーバー - テストヘッダー**&#x200B;ルールを、ルーティングルールの&#x200B;**兄弟**&#x200B;として（同じレベルで）作成します。ルーティングルールの内部にネスト&#x200B;**しない**&#x200B;でください。 Akamai Property Manager のルールツリーでは、階層は次のようになります。
>
>```
>▼ Optimize at Edge                         ← parent rule group
>   ▼ Optimize at Edge Routing               ← routing child
>       Site Failover Behavior                 ← nested child
>   EdgeOptimize Failover - Test Header      ← sibling of routing child
>```
>
>兄弟ルールは、Akamaiが元のホスト名に対して失敗したリクエストを再作成したときに評価されます。 ルーティングルールのAPI キー条件により、そのリクエストがEdge Optimizeに再度送信されるのを防ぐことができます。
>
>また、**Edge での最適化ルーティング**&#x200B;ルールが、同じリクエストのオリジン、キャッシュ動作、キャッシュ ID を変更する後続の一致ルールにより上書きされないことも確保されます。 別の一致ルールにより、これらの動作がリセットされた場合、Edge での最適化ルーティングまたはキャッシュが期待どおりに機能しないことがあります。

![ フェールオーバー応答ヘッダールールを設定](/help/assets/optimize-at-edge/akamai-step9-failover-header.png)

サイトフェールオーバーは、Edge Optimizeがエラーを返したりタイムアウトしたりした場合、訪問者がサイトの通常の応答を引き続き受け取るように、元のホスト名のリクエストをAkamaiが再作成します。

| シナリオ | 動作 |
| --- | --- |
| Edge での最適化で `2XX` または `3XX` が返される | 最適化された応答が提供されます。 `x-edgeoptimize-request-id`が存在します。 |
| Edge Optimizeが`4XX`～`5XX`を返すか、オリジンがタイムアウトします | リクエストは、元のホスト名に対して再作成されます。 応答に`x-edgeoptimize-fo: true`が含まれています。 |

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
| `x-edgeoptimize-fo` | フェイルオーバーが発生した場合のみ存在（値：`true`） | 不在 |

{{verify-routing-status-in-ui}}

{{return-to-overview}}
