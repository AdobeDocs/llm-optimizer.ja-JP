---
title: Edge での最適化
description: オーサリングの変更を必要とせずに、CDN Edge で LLM Optimizer の最適化を実現する方法について説明します。
feature: Opportunities
source-git-commit: 1f665bd14349c15d92f8274742606abcf9b02000
workflow-type: tm+mt
source-wordcount: '4708'
ht-degree: 44%

---


# Edge での最適化

このページでは、オーサリングを変更せずに、CDN Edge で最適化を実現する方法の詳細な概要について説明します。 オンボーディングプロセス、使用可能な最適化の機会、Edge 自動最適化の方法について説明します。

>[!NOTE]
>この機能は現在、早期アクセス版です。 早期アクセスプログラムについて詳しくは、[こちら](https://experienceleague.adobe.com/ja/docs/experience-manager-cloud-service/content/release-notes/release-notes/release-notes-current#aem-beta-programs)を参照してください。

## Edge での最適化とは

Edge 最適化は、LLM ユーザーエージェントに対して AI に適した変更を提供する、LLM Optimizer のエッジベースのデプロイメント機能です。 現在のコンテキストでは、「Edge」は、CDN レイヤーで最適化が適用されることを意味します。 CDN レイヤーで最適化が行われるので、コンテンツ管理システム（CMS）でのオーサリング変更は必要なく、元の CMS は変更されません。 この分離により、既存の公開ワークフローを変更することなく、LLM の可視性を向上させることができます。 これは、エージェントトラフィックのみをターゲットにし、人間のユーザーや SEO ボットには影響を与えません。 LLM Optimizer がページを最適化する機会を検出すると、ユーザーは CDN Edge で直接修正をデプロイできます。

Edge での最適化は、複雑なエンジニアリング作業を必要とする従来の修正に代わる、より高速で効率的な代替手段です。 前述のとおり、1 回限りの設定を完了すると、変更の適用にプラットフォームの変更や長い開発サイクルは必要ありません。 開発者のエンゲージメントを必要とせずに、改善点を数分で公開できます。 これは、コードなしで web サイトを AI エージェント用に最適化する方法です。

Edge での最適化は、マーケティング、SEO、コンテンツおよびデジタル戦略の各チームのビジネスユーザー向けに設計されています。 これにより、ビジネスユーザーは、LLM Optimizer で機会の特定、候補の理解、修正の簡単なデプロイまで、すべてのジャーニーを完了できます。 Edge での最適化を使用すると、ユーザーは変更をプレビューし、CDN Edge ですばやくデプロイして、最適化がライブであることを検証できます。 パフォーマンスは、LLM Optimizer エコシステムで追跡できます。

### 主なメリット

* **AI のみの配信：**&#x200B;人間の訪問者や SEO ボットに影響を与えることなく、最適化された HTML を AI エージェントにのみ配信します。
* **サイクルの高速化：**&#x200B;変更を数週間ではなく数分で公開します。 プラットフォームの変更や長いエンジニアリングサイクルは必要ありません。
* **元に戻せる：**&#x200B;数分でページを元に戻せるワンクリックロールバック機能がサポートされています。
* **パフォーマンスへの影響なし：** Edge ベースの最適化とキャッシュにより、サイトの待ち時間は影響を受けません。
* **CDN と CMS に依存しない：**&#x200B;コンテンツ管理システムに関係なく、任意の CDN 設定およびフロントエンド設定と連携します。

### Edge での最適化でサポートされる機会

Edge での最適化では、エージェント型の web エクスペリエンスを向上できる機会がサポートされます。 各機会について詳しくは、[オポチュニティダッシュボード](/help/dashboards/opportunities.md)ページと現在のページの機会の節の両方を参照してください。

## オンボーディング

オンボーディングプロセスを開始するには、アドビのアカウントチームまたは FDE チームに問い合わせる必要があります。 また、IT チームまたは CDN チームも、前提条件と設定プロセスを完了する必要があります。 さらに、オンボーディングのサポートが必要な場合は、`llmo-at-edge@adobe.com` までお問い合わせください。

Edge での最適化にオンボードする際の前提条件：

* LLM Optimizer へのオンボーディングプロセスを完了します。
* CDN ログのログ転送プロセスを完了します。

IT／CDN チームの要件：
* サイト `*AdobeEdgeOptimize/1.0*`robots.txt ファイルまたはボットトラフィック管理ルールの許可リストに user-agent を追加します。
* ページがドメインレベルや CDN レベルでブロックされていないことを確認します。
* CDN に Edge での最適化ルーティングルールを追加します。
* LLM Optimizer インターフェイスで Edge での最適化ルーティングを確認します。

設定プロセスのガイドに、いくつかの CDN 設定のサンプル設定を以下に示します。 これらの例は、実際のライブ設定に合わせて調整する必要があることに注意してください。 最初に下位環境で変更を適用することをお勧めします。

>[!BEGINTABS]

>[!TAB AEM Cloud Service の管理による CDN （Fastly） ]

**Edgeの最適化 – AEM Cloud Service 管理 CDN （Fastly）**

この設定により、エージェンティックトラフィック（AI ボットや LLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 人間の訪問者と SEO ボットは、通常どおりオリジンから提供され続けます。 設定をテストするには、セットアップが完了した後、応答でヘッダー `x-edgeoptimize-request-id` を探します。

**前提条件**

エージェンティックトラフィックのEdgeへのルーティングを開始するには最適化：

1. **顧客設定** に移動し、「**CDN 設定**」タブを選択します。

   ![ 顧客設定に移動 ](/help/assets/optimize-at-edge/prereq-customer-config-nav.png)

2. 「**最適化をデプロイするための AI トラフィックルーティング**」で、「**最適化を AI エージェントにデプロイ**」チェックボックスをオンにします。 Adobe チームが、お客様に代わってルーティング設定を処理します。

   ![AI エージェントへのティックのデプロイの最適化 ](/help/assets/optimize-at-edge/prereq-deploy-checkbox.png)

3. チェックボックスを有効にすると、ステータスにセットアップが進行中であることが示されます。 Adobe チームがルーティング設定を行います。

   ![AI トラフィックルーティングの設定中 ](/help/assets/optimize-at-edge/prereq-traffic-routing-progress.png)

   ルーティングが設定され、アクティブになると、ステータスが更新され、ルーティングが正常に有効になったことを示す緑色のチェックマークが表示されます。 ユーザー側でこれ以上のアクションを行う必要はありません。

さらに、上記の手順に関するヘルプが必要な場合は、Adobe アカウントチームまたは `llmo-at-edge@adobe.com` に問い合わせてください。

**Cloud Manager パイプラインを介したセルフサービスルーティング**

Cloud Manager パイプラインを使用して自分でルーティングを設定する場合は、次の手順に従います。 ルーティング設定は、[originSelector CDN ルール](https://experienceleague.adobe.com/ja/docs/experience-manager-cloud-service/content/implementing/content-delivery/cdn-configuring-traffic#origin-selectors)を使用して行われます。 前提条件は、次のとおりです。

* ルーティングするドメインを決定します。
* ルーティングするパスを決定します。
* ルーティングするユーザーエージェントを決定します（推奨される正規表現）。

ルールをデプロイするには、次の操作を実行する必要があります。

* [ 設定パイプライン ](https://experienceleague.adobe.com/ja/docs/experience-manager-cloud-service/content/operations/config-pipeline) を作成します。
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

**設定の確認**

設定をテストするには、curl を実行し、次の結果を確認します。

```
curl -svo /dev/null https://www.example.com/page.html --header "user-agent: chatgpt-user"
< HTTP/2 200
< x-edgeoptimize-request-id: 50fce12d-0519-4fc6-af78-d928785c1b85
```

トラフィックルーティングのステータスは、LLM Optimizer UI でも確認できます。 **顧客設定** に移動し、「**CDN 設定**」タブを選択します。

![ ルーティングを有効にした AI トラフィックルーティングステータス ](/help/assets/optimize-at-edge/adobe-CDN-traffic-routed-tick.png)

>[!TAB Fastly（BYOCDN）]

**Edge最適化 – Fastly （BYOCDN）**

この設定により、エージェンティックトラフィック（AI ボットや LLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 人間の訪問者と SEO ボットは、通常どおりオリジンから提供され続けます。 設定をテストするには、セットアップが完了した後、応答でヘッダー `x-edgeoptimize-request-id` を探します。

**前提条件**

Fastly VCL ルールを設定する前に、以下が揃っていることを確認します。

* お使いのドメインの Fastly へのアクセス。
* LLM Optimizerのオンボーディングプロセスを完了しました。
* LLM Optimizerへの CDN ログ転送が完了しました。
* LLM Optimizer UI から取得されたEdge最適化 API キー。

**API キーを取得する手順：**

1. **顧客設定** に移動し、「**CDN 設定**」タブを選択します。

   ![ 顧客設定に移動 ](/help/assets/optimize-at-edge/prereq-customer-config-nav.png)

2. 「**最適化をデプロイするための AI トラフィックルーティング**」で、「**最適化を AI エージェントにデプロイ**」チェックボックスをオンにします。

   ![AI エージェントへのティックのデプロイの最適化 ](/help/assets/optimize-at-edge/prereq-deploy-checkbox.png)

3. API キーをコピーして、以下のルーティング設定手順に進みます。

   ![API キーをコピーします ](/help/assets/optimize-at-edge/prereq-copy-api-key.png)

   >[!NOTE]
   >この段階では、ステータスに、設定がまだ完了していないことを示す赤い十字が表示される場合があります。 これは想定されています。以下のルーティング設定を完了し、AI ボットトラフィックのフローが開始されると、ステータスが緑色のチェックマークに更新され、ルーティングが正常に有効であることが確認されます。

さらに、上記の手順に関するヘルプが必要な場合は、Adobe アカウントチームまたは `llmo-at-edge@adobe.com` に問い合わせてください。

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

**設定の確認**

設定をテストするには、curl を実行し、次の結果を確認します。

```
curl -svo /dev/null https://www.example.com/page.html --header "user-agent: chatgpt-user"
< HTTP/2 200
< x-edgeoptimize-request-id: 50fce12d-0519-4fc6-af78-d928785c1b85
```

トラフィックルーティングのステータスは、LLM Optimizer UI でも確認できます。 **顧客設定** に移動し、「**CDN 設定**」タブを選択します。

![ ルーティングを有効にした AI トラフィックルーティングステータス ](/help/assets/optimize-at-edge/byocdn-CDN-traffic-routed-tick.png)

>[!TAB Akamai（BYOCDN）]

**Edgeの最適化 – Akamai （BYOCDN）**

この設定により、エージェンティックトラフィック（AI ボットや LLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 人間の訪問者と SEO ボットは、通常どおりオリジンから提供され続けます。 設定をテストするには、セットアップが完了した後、応答でヘッダー `x-edgeoptimize-request-id` を探します。

**前提条件**

Akamai Property Manager ルールを設定する前に、次のことを確認してください。

* ドメインの Akamai Property Manager にアクセスします。
* LLM Optimizerのオンボーディングプロセスを完了しました。
* LLM Optimizerへの CDN ログ転送が完了しました。
* LLM Optimizer UI から取得されたEdge最適化 API キー。

**API キーを取得する手順：**

1. **顧客設定** に移動し、「**CDN 設定**」タブを選択します。

   ![ 顧客設定に移動 ](/help/assets/optimize-at-edge/prereq-customer-config-nav.png)

2. 「**最適化をデプロイするための AI トラフィックルーティング**」で、「**最適化を AI エージェントにデプロイ**」チェックボックスをオンにします。

   ![AI エージェントへのティックのデプロイの最適化 ](/help/assets/optimize-at-edge/prereq-deploy-checkbox.png)

3. API キーをコピーして、以下のルーティング設定手順に進みます。

   ![API キーをコピーします ](/help/assets/optimize-at-edge/prereq-copy-api-key.png)

   >[!NOTE]
   >この段階では、ステータスに、設定がまだ完了していないことを示す赤い十字が表示される場合があります。 これは想定されています。以下のルーティング設定を完了し、AI ボットトラフィックのフローが開始されると、ステータスが緑色のチェックマークに更新され、ルーティングが正常に有効であることが確認されます。

さらに、上記の手順に関するヘルプが必要な場合は、Adobe アカウントチームまたは `llmo-at-edge@adobe.com` に問い合わせてください。

**設定**

次の Akamai Property Manager ルールは、LLM ユーザーエージェントをEdge Optimize にルーティングします。 設定には、次の手順が含まれます。

**1. ルーティング条件を設定（ユーザーエージェントの一致）**

次のユーザーエージェントのルーティングを設定します。

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

ヘッダー `x-forwarded-host``{{builtin.AK_HOST}}` に設定

![ 送信リクエストヘッダーの変更 ](/help/assets/optimize-at-edge/akamai-step8-outgoing-request.png)

**9. サイトフェイルオーバー**

![サイトフェイルオーバー](/help/assets/optimize-at-edge/akamai-step9-failover.png)

![フェイルオーバー動作](/help/assets/optimize-at-edge/akamai-step9-failover-behaviors.png)

![フェイルオーバールール](/help/assets/optimize-at-edge/akamai-step9-failover-rules.png)

サイトのフェイルオーバーにより、Edge Optimize から `4XX` または `5XX` エラーが返された場合に、リクエストがデフォルトのオリジンに自動的にルーティングされ、エンドユーザーは引き続きレスポンスを受け取ることができます。

| シナリオ | 動作 |
| --- | --- |
| Edge最適化による `2XX` の返り値 | 最適化された応答がクライアントに提供されます。 |
| Edge Optimize は、`4XX` または `5XX` を返します。 | リクエストはデフォルトのオリジンにルーティングされます。 |

**設定の確認**

設定をテストするには、curl を実行し、次の結果を確認します。

```
curl -svo /dev/null https://www.example.com/page.html --header "user-agent: chatgpt-user"
< HTTP/2 200
< x-edgeoptimize-request-id: 50fce12d-0519-4fc6-af78-d928785c1b85
```

トラフィックルーティングのステータスは、LLM Optimizer UI でも確認できます。 **顧客設定** に移動し、「**CDN 設定**」タブを選択します。

![ ルーティングを有効にした AI トラフィックルーティングステータス ](/help/assets/optimize-at-edge/byocdn-CDN-traffic-routed-tick.png)

>[!TAB Cloudflare （BYOCDN） ]

**Edge Optimize - Cloudflare （BYOCDN）**

この設定により、エージェンティックトラフィック（AI ボットや LLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 人間の訪問者と SEO ボットは、通常どおりオリジンから提供され続けます。 設定をテストするには、セットアップが完了した後、応答でヘッダー `x-edgeoptimize-request-id` を探します。

**前提条件**

Cloudflare Worker のルーティングルールを設定する前に、以下を確認します。

* お使いのドメインでワーカーが有効になっている Cloudflare アカウント。
* Cloudflare でドメインの DNS 設定にアクセスします。
* LLM Optimizerのオンボーディングプロセスを完了しました。
* LLM Optimizerへの CDN ログ転送が完了しました。
* LLM Optimizer UI から取得されたEdge最適化 API キー。

**API キーを取得する手順：**

1. **顧客設定** に移動し、「**CDN 設定**」タブを選択します。

   ![ 顧客設定に移動 ](/help/assets/optimize-at-edge/prereq-customer-config-nav.png)

2. 「**最適化をデプロイするための AI トラフィックルーティング**」で、「**最適化を AI エージェントにデプロイ**」チェックボックスをオンにします。

   ![AI エージェントへのティックのデプロイの最適化 ](/help/assets/optimize-at-edge/prereq-deploy-checkbox.png)

3. API キーをコピーして、以下のルーティング設定手順に進みます。

   ![API キーをコピーします ](/help/assets/optimize-at-edge/prereq-copy-api-key.png)

   >[!NOTE]
   >この段階では、ステータスに、設定がまだ完了していないことを示す赤い十字が表示される場合があります。 これは想定されています。以下のルーティング設定を完了し、AI ボットトラフィックのフローが開始されると、ステータスが緑色のチェックマークに更新され、ルーティングが正常に有効であることが確認されます。

さらに、上記の手順に関するヘルプが必要な場合は、Adobe アカウントチームまたは `llmo-at-edge@adobe.com` に問い合わせてください。

**ルーティングの仕組み**

正しく設定されると、Agentic User Agent からのドメイン（`www.example.com/page.html` など）へのリクエストが Cloudflare Worker によってインターセプトされ、Edge Optimize バックエンドにルーティングされます。 バックエンドリクエストには、必要なヘッダーが含まれます。

**バックエンドリクエストのテスト**

Edgeの Optimize バックエンドに対して直接リクエストを行うことで、ルーティングを確認できます。

```
curl -svo /dev/null https://live.edgeoptimize.net/page.html \
  -H 'x-forwarded-host: www.example.com' \
  -H 'x-edgeoptimize-url: /page.html' \
  -H 'x-edgeoptimize-api-key: $EDGE_OPTIMIZE_API_KEY' \
  -H 'x-edgeoptimize-config: LLMCLIENT=TRUE;'
```

**必須ヘッダー**

Edge Optimize バックエンドへのリクエストでは、次のヘッダーを設定する必要があります。

| ヘッダー | 説明 | 例 |
|--------|-------------|---------|
| `x-forwarded-host` | リクエストの元のホスト。 サイトドメインを識別するために必要です。 | `www.example.com` |
| `x-edgeoptimize-url` | リクエストの元の URL パスとクエリ文字列。 | `/page.html` または `/products?id=123` |
| `x-edgeoptimize-api-key` | ドメイン用にAdobeから提供される API キー。 | `your-api-key-here` |
| `x-edgeoptimize-config` | キャッシュキーを区別するための設定文字列。 | `LLMCLIENT=TRUE;` |

**手順 1:Cloudflare Worker の作成**

1. Cloudflare ダッシュボードにログインします。
2. サイドバーの **ワーカーとページ** に移動します。
3. **アプリケーションを作成** をクリックしてから、**ワーカーを作成** をクリックします。
4. ワーカーに名前を付けます（例：`edge-optimize-router`）。
5. 「**デプロイ**」をクリックして、デフォルトのコードを使用してワーカーを作成します。

![Cloudflare Workers ダッシュボード ](/help/assets/optimize-at-edge/cloudflare-workers-dashboard.png)

**手順 2：ワーカーコードの追加**

ワーカーを作成したら、「**コードを編集**」をクリックして、デフォルトのコードを以下に置き換えます。

```javascript
/**
 * Edge Optimize BYOCDN - Cloudflare Worker
 *
 * This worker routes requests from agentic bots (AI/LLM user agents) to the
 * Edge Optimize backend service for optimized content delivery.
 *
 * Features:
 * - Routes agentic bot traffic to Edge Optimize backend
 * - Failover to origin on Edge Optimize errors (any 4XX or 5XX errors)
 * - Loop protection to prevent infinite redirects
 * - Human visitors and SEO bots are served from the origin as usual
 */

// List of agentic bot user agents to route to Edge Optimize
const AGENTIC_BOTS = [
  'AdobeEdgeOptimize-AI',
  'ChatGPT-User',
  'GPTBot',
  'OAI-SearchBot',
  'PerplexityBot',
  'Perplexity-User'
];

// Targeted paths for Edge Optimize routing
// Set to null to route all HTML pages, or specify an array of paths
const TARGETED_PATHS = null; // e.g., ['/', '/page.html', '/products']

// Failover configuration
// Failover on any 4XX client error or 5XX server error from Edge Optimize
const FAILOVER_ON_4XX = true; // Failover on any 4XX error (400-499)
const FAILOVER_ON_5XX = true; // Failover on any 5XX error (500-599)

export default {
  async fetch(request, env, ctx) {
    return await handleRequest(request, env, ctx);
  },
};

async function handleRequest(request, env, ctx) {
  const url = new URL(request.url);
  const userAgent = request.headers.get("user-agent")?.toLowerCase() || "";

  // Check if request is already processed (loop protection)
  const isEdgeOptimizeRequest = !!request.headers.get("x-edgeoptimize-request");

  // Construct the original path and query string
  const pathAndQuery = `${url.pathname}${url.search}`;

  // Check if the path matches HTML pages (no extension or .html extension)
  const isHtmlPage = /(?:\/[^./]+|\.html|\/)$/.test(url.pathname);

  // Check if path is in targeted paths (if specified)
  const isTargetedPath = TARGETED_PATHS === null
    ? isHtmlPage
    : (isHtmlPage && TARGETED_PATHS.includes(url.pathname));

  // Check if user agent is an agentic bot
  const isAgenticBot = AGENTIC_BOTS.some((ua) => userAgent.includes(ua.toLowerCase()));

  // Route to Edge Optimize if:
  // 1. Request is NOT already from Edge Optimize (prevents infinite loops)
  // 2. User agent matches one of the agentic bots
  // 3. Path is targeted for optimization
  if (!isEdgeOptimizeRequest && isAgenticBot && isTargetedPath) {

    // Build the Edge Optimize request URL
    const edgeOptimizeURL = `https://live.edgeoptimize.net${pathAndQuery}`;

    // Clone and modify headers for the Edge Optimize request
    const edgeOptimizeHeaders = new Headers(request.headers);

    // Remove any existing Edge Optimize headers for security
    edgeOptimizeHeaders.delete("x-edgeoptimize-api-key");
    edgeOptimizeHeaders.delete("x-edgeoptimize-url");
    edgeOptimizeHeaders.delete("x-edgeoptimize-config");

    // x-forwarded-host: The original site domain
    // Use environment variable if set, otherwise use the request host
    edgeOptimizeHeaders.set("x-forwarded-host", env.EDGE_OPTIMIZE_TARGET_HOST ?? url.host);

    // x-edgeoptimize-api-key: Your Adobe-provided API key
    edgeOptimizeHeaders.set("x-edgeoptimize-api-key", env.EDGE_OPTIMIZE_API_KEY);

    // x-edgeoptimize-url: The original request URL path and query
    edgeOptimizeHeaders.set("x-edgeoptimize-url", pathAndQuery);

    // x-edgeoptimize-config: Configuration for cache key differentiation
    edgeOptimizeHeaders.set("x-edgeoptimize-config", "LLMCLIENT=TRUE;");

    try {
      // Send request to Edge Optimize backend
      const edgeOptimizeResponse = await fetch(new Request(edgeOptimizeURL, {
        headers: edgeOptimizeHeaders,
        redirect: "manual", // Preserve redirect responses from Edge Optimize
      }), {
        cf: {
          cacheEverything: true, // Enable caching based on origin's cache-control headers
        },
      });

      // Check if we need to failover to origin
      const status = edgeOptimizeResponse.status;
      const is4xxError = FAILOVER_ON_4XX && status >= 400 && status < 500;
      const is5xxError = FAILOVER_ON_5XX && status >= 500 && status < 600;

      if (is4xxError || is5xxError) {
        console.log(`Edge Optimize returned ${status}, failing over to origin`);
        return await failoverToOrigin(request, env, url);
      }

      // Return the Edge Optimize response
      return edgeOptimizeResponse;

    } catch (error) {
      // Network error or timeout - failover to origin
      console.log(`Edge Optimize request failed: ${error.message}, failing over to origin`);
      return await failoverToOrigin(request, env, url);
    }
  }

  // For all other requests (human users, SEO bots), pass through unchanged
  return fetch(request);
}

/**
 * Failover to origin server when Edge Optimize returns an error
 * @param {Request} request - The original request
 * @param {Object} env - Environment variables
 * @param {URL} url - Parsed URL object
 */
async function failoverToOrigin(request, env, url) {
  // Build origin URL
  const originURL = `${url.protocol}//${env.EDGE_OPTIMIZE_TARGET_HOST}${url.pathname}${url.search}`;

  // Prepare headers - clean Edge Optimize headers and add loop protection
  const originHeaders = new Headers(request.headers);
  originHeaders.set("Host", env.EDGE_OPTIMIZE_TARGET_HOST);
  originHeaders.delete("x-edgeoptimize-api-key");
  originHeaders.delete("x-edgeoptimize-url");
  originHeaders.delete("x-edgeoptimize-config");
  originHeaders.delete("x-forwarded-host");
  originHeaders.set("x-edgeoptimize-request", "fo");

  // Create and send origin request
  const originRequest = new Request(originURL, {
    method: request.method,
    headers: originHeaders,
    body: request.body,
    redirect: "manual",
  });

  const originResponse = await fetch(originRequest);

  // Add failover marker header to response
  const modifiedResponse = new Response(originResponse.body, {
    status: originResponse.status,
    statusText: originResponse.statusText,
    headers: originResponse.headers,
  });
  modifiedResponse.headers.set("x-edgeoptimize-fo", "1");
  return modifiedResponse;
}
```

**保存してデプロイ** をクリックして、ワーカーを公開します。

![Cloudflare Worker コードエディター ](/help/assets/optimize-at-edge/cloudflare-worker-editor.png)

**手順 3：環境変数の設定**

環境変数は、API キーなどの機密性の高い設定を安全に保存します。

1. ワーカーの設定で、**設定**/**変数** に移動します。
2. **環境変数** の下の **変数を追加** をクリックします。
3. 次の変数を追加します。

   | 変数名 | 説明 | 必須 |
   |---------------|-------------|----------|
   | `EDGE_OPTIMIZE_API_KEY` | Adobe提供のEdge Optimize API キー。 | はい |
   | `EDGE_OPTIMIZE_TARGET_HOST` | Edge Optimize リクエスト（ヘッダーとして送信）のターゲットホスト `x-forwarded-host`、フェイルオーバーのオリジンドメイン。 プロトコルを使用しないドメインのみを指定する必要があります（例：`https://www.example.com` ではなく `www.example.com`）。 | はい |

4. API キーの場合は、「**暗号化**」をクリックして、安全に保存します。
5. **保存してデプロイ** をクリックします。

![Cloudflare 環境変数 ](/help/assets/optimize-at-edge/cloudflare-env-variables.png)

**手順 4：ドメインへのルートの追加**

ドメインでワーカーをアクティブ化するには：

1. ワーカーの **設定**/**トリガー** に移動します。
2. **ルート** の下の **ルートを追加** をクリックします。
3. ドメインパターン（例：`www.example.com/*` または `example.com/*`）を入力します。
4. ドロップダウンからゾーンを選択します。
5. 「**保存**」をクリックします。

または、ゾーン レベルでルートを構成することもできます。

1. Cloudflare のドメインに移動します。
2. **ワーカールート** に移動します。
3. **ルートを追加** をクリックし、パターンとワーカーを指定します。

![Cloudflare ワーカールート ](/help/assets/optimize-at-edge/cloudflare-worker-routes.png)

**手順 5：設定を確認する**

デプロイ後、エージェントを使用してリクエストを行い、設定をテストします。

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: chatgpt-user"
```

成功した応答には、`x-edgeoptimize-request-id` のヘッダーが含まれます。

```
< HTTP/2 200
< x-edgeoptimize-request-id: 50fce12d-0519-4fc6-af78-d928785c1b85
```

トラフィックルーティングのステータスは、LLM Optimizer UI でも確認できます。 **顧客設定** に移動し、「**CDN 設定**」タブを選択します。

![ ルーティングを有効にした AI トラフィックルーティングステータス ](/help/assets/optimize-at-edge/byocdn-CDN-traffic-routed-tick.png)

また、通常のトラフィックが引き続き動作することを確認することもできます。

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: Mozilla/5.0"
```

このリクエストは、`x-edgeoptimize-request-id` ヘッダーを使用せずに、オリジンから提供する必要があります。

**フェイルオーバー動作の確認**

Edge Optimize が使用できない場合やエラーが返された場合、ワーカーは自動的にオリジンにフェイルオーバーします。 フェイルオーバー応答には、`x-edgeoptimize-fo` ヘッダーが含まれます。

```
< HTTP/2 200
< x-edgeoptimize-fo: 1
```

Cloudflare Workers ログでフェイルオーバーイベントを監視して、問題のトラブルシューティングを行うことができます。

**ワーカーロジックについて**

Cloudflare Worker は、次のロジックを実装します。

1. **ユーザーエージェントの検出：** 受信リクエストのユーザーエージェントが、定義されたエージェンティックボットのいずれかと一致するかどうかを確認します（大文字と小文字を区別しません）。

2. **パスのターゲット設定：** オプションで、ターゲットパスに基づいてリクエストをフィルタリングします。 デフォルトでは、すべてのHTML ページ（`/`、拡張子なし、`.html` で終わる URL）がルーティングされます。 `TARGETED_PATHS` 配列を使用して、特定のパスを指定できます。

3. **ループ保護：** `x-edgeoptimize-request` ヘッダーは、無限ループを防ぎます。 Edge Optimize がオリジンにリクエストを戻す場合、このヘッダーは `"1"` に設定され、ワーカーはEdge Optimize にリクエストをルーティングせずに通過させます。

4. **Header security:** Edge Optimize ヘッダーを設定する前に、Header Injection 攻撃を防ぐために、ワーカーは受信リクエストから既存の `x-edgeoptimize-*` ヘッダーを削除します。

5. **ヘッダーマッピング：** ワーカーは、Edge Optimize に必要なヘッダーを設定します。
   * `x-forwarded-host` – 元のサイトドメインを識別します。
   * `x-edgeoptimize-url` – 元のリクエストパスとクエリ文字列を保持します。
   * `x-edgeoptimize-api-key` - Edge Optimize でリクエストを認証します。
   * `x-edgeoptimize-config` - キャッシュキーを設定します。

6. **フェイルオーバーロジック：** Edge Optimize からエラーステータスコード（4XX クライアントエラーまたは 5XX サーバーエラー）が返された場合、またはネットワークエラーが原因でリクエストが失敗した場合、ワーカーは `EDGE_OPTIMIZE_TARGET_HOST` を使用して、自動的にオリジンにフェイルオーバーします。 フェイルオーバー応答には、フェイルオーバーが発生したことを示す `x-edgeoptimize-fo: 1` ヘッダーが含まれます。

7. **リダイレクト処理：** 「`redirect: "manual"`」オプションを使用すると、Edge Optimize からのリダイレクト応答が、ワーカーが従わずにクライアントに確実に渡されます。

**設定のカスタマイズ**

ワーカーの動作をカスタマイズするには、コードの上部にある設定定数を変更します。

**Agentic ボットリスト**

ユーザーエージェントを追加または削除するには、`AGENTIC_BOTS` のアレイを変更します。

```javascript
const AGENTIC_BOTS = [
  'AdobeEdgeOptimize-AI',
  'ChatGPT-User',
  'GPTBot',
  'OAI-SearchBot',
  'PerplexityBot',
  'Perplexity-User',
  // Add additional user agents as needed
  'ClaudeBot',
  'Anthropic-AI'
];
```

**ターゲットパス**

デフォルトでは、すべてのHTMLページがEdge Optimize にルーティングされます。 特定のパスへのルーティングを制限するには、`TARGETED_PATHS` の配列を変更します。

```javascript
// Route all HTML pages (default)
const TARGETED_PATHS = null;

// Or specify exact paths to route
const TARGETED_PATHS = ['/', '/page.html', '/products', '/about-us'];
```

**フェイルオーバー構成**

デフォルトでは、Edge Optimize から 4XX または 5XX のエラーが発生した場合にワーカーがフェイルオーバーします。 この動作をカスタマイズします。

```javascript
// Default: failover on any 4XX or 5XX error
const FAILOVER_ON_4XX = true;
const FAILOVER_ON_5XX = true;

// Failover only on 5XX server errors (not 4XX client errors)
const FAILOVER_ON_4XX = false;
const FAILOVER_ON_5XX = true;

// Disable automatic failover (not recommended)
const FAILOVER_ON_4XX = false;
const FAILOVER_ON_5XX = false;
```

**重要な検討事項**

* **フェイルオーバー動作：** Edge Optimize からエラー（4XX または 5XX ステータスコード）が返された場合、またはネットワークエラーが原因でリクエストが失敗した場合、ワーカーは自動的にオリジンにフェイルオーバーします。 フェイルオーバーでは、`EDGE_OPTIMIZE_TARGET_HOST` をオリジンドメインとして使用します（Fastly の `F_Default_Origin` や CloudFront の `Default_Origin` と同様）。 フェイルオーバー応答には、監視とデバッグに使用できる `x-edgeoptimize-fo: 1` ヘッダーが含まれています。

* **キャッシュ：** Cloudflare は、デフォルトで URL に基づいて応答をキャッシュします。 エージェンティックトラフィックは人間によるトラフィックとは異なるコンテンツを受け取るので、キャッシュ設定に責任があることを確認してください。 キャッシュ API またはキャッシュヘッダーを使用して、キャッシュされたコンテンツを区別することを検討します。 `x-edgeoptimize-config` ヘッダーをキャッシュキーに含める必要があります。

* **レート制限：** Edgeのモニタリング使用状況を最適化し、必要に応じてエージェンティックトラフィックにレート制限を実装することを検討します。

* **テスト：** 実稼動環境にデプロイする前に、常にステージング環境で設定をテストします。 エージェンシーと人間の両方のトラフィックが期待どおりに動作することを確認します。 Edge Optimize エラーをシミュレートして、フェイルオーバー動作をテストします。

* **ログ：** Cloudflare Workers のログを有効にして、リクエストの監視や問題のトラブルシューティングを行います。 **ワーカー** / **ワーカー** / **ログ** に移動して、リアルタイムログを表示します。 ワーカーは、デバッグ目的でフェイルオーバーイベントをログに記録します。

**トラブルシューティング**

| 問題 | 考えられる原因 | 解決策 |
|-------|----------------|----------|
| 応答に `x-edgeoptimize-request-id` ヘッダーがありません | 作業者ルートが一致していないか、エージェント ボット リストにユーザーエージェントがありません。 | ルートパターンがリクエスト URL と一致することを確認します。 ユーザーエージェントが `AGENTIC_BOTS` 配列にあることを確認します。 |
| Edge Optimize の 401 または 403 エラー | API キーが無効または見つかりません。 | 環境変数 `EDGE_OPTIMIZE_API_KEY` 正しく設定されていることを確認します。 Adobeに問い合わせて、API キーがアクティブであることを確認します。 |
| 無限リダイレクトまたはループ | ループ保護ヘッダーが正しく設定またはチェックされていない。 | `x-edgeoptimize-request` ヘッダーチェックが行われていることを確認します。 |
| 影響を受ける人的トラフィック | ワーカールーティングロジックが広すぎます。 | ユーザーエージェントのマッチングロジックが正しく、大文字と小文字が区別されないことを確認します。 `TARGETED_PATHS` が正しく設定されていることを確認します。 |
| 応答時間が遅い | Edgeへのネットワーク遅延バックエンドを最適化します。 | これは最初のリクエストで想定されます。後続のリクエストは、Edge Optimize でキャッシュされます。 |
| 応答の `x-edgeoptimize-fo: 1` ヘッダー | Edge Optimize がエラーを返し、オリジンへのフェールオーバーが発生しました。 | 特定のエラーコードについては、Cloudflare Workers のログを確認してください。 AdobeでEdge最適化サービスのステータスを確認します。 |
| フェイルオーバーが機能しない | フェールオーバーフラグが無効になっているか、フェールオーバーロジックでエラーが発生しています。 | `FAILOVER_ON_4XX` と `FAILOVER_ON_5XX` が `true` に設定されていることを確認します。 ワーカーログでエラーメッセージを確認します。 |
| 特定のパスが最適化されていない | ターゲットパスまたはHTMLページパターンと一致しないパス。 | パスが `TARGETED_PATHS` 内にあり（指定されている場合）、HTML ページの正規表現パターンと一致することを確認します。 |
| リクエストが無効なホストで失敗する | `EDGE_OPTIMIZE_TARGET_HOST` にはプロトコル（例：`https://`）が含まれます。 | プロトコルを使用しないドメイン名のみを使用します（例：`https://example.com` ではなく `example.com`）。 |
| フェイルオーバー時の 530 エラー | Cloudflare がオリジンに接続できないか、フェイルオーバーリクエストに無効なヘッダーがあります。 | フェイルオーバー機能によってEdge Optimize ヘッダーが削除されるようにします。 オリジンがアクセス可能であり、DNS が正しく設定されていることを確認します。 |

>[!ENDTABS]

>[!NOTE]
>その他の CDN プロバイダーについては、`llmo-at-edge@adobe.com` に連絡して、IT／CDN チームのオンボーディングをサポートしてください。 設定が完了したら、LLM Optimizer で Edge での最適化の機会に関する候補をデプロイできます。

## オポチュニティ

次の表に、エージェント型の web エクスペリエンスを向上できる機会と、Edge での最適化でサポートされる機会を示します。

| オポチュニティ | タイプ | 自動特定 | 自動提案 | 自動最適化 |
|---------|----------|----------|----------|----------|
| コンテンツの可視性を回復 | テクニカル GEO | 重要なコンテンツが AI エージェントから非表示になっているページを検出します。 影響を受ける URL と回復可能な予想されるコンテンツを表示します。 | AI エージェントに使用できるコンテンツをハイライト表示し、これらのページの事前レンダリングを有効にすることをお勧めします。 | 以前は非表示になっていたコンテンツを回復するエージェントトラフィックに、完全にレンダリングされた AI 対応の HTML スナップショットを提供します。 |
| LLM 対応の要約の追加 | コンテンツの最適化 | ページレベルやセクションレベルで簡潔な要約がなく、AI がすばやくスキャンして理解するのが難しくなる長いページや複雑なページを特定します。 | 主要なコンテンツをキャプチャするページレベルおよびセクションレベルでの短い AI 生成の要約をお勧めします。 | 関連する HTML セクションに要約を挿入し、モデルがページコンテンツを解釈および記述する方法を改善します。 |
| 関連する FAQ の追加 | コンテンツの最適化 | FAQ からメリットが得られる可能性のある、既存のページコンテンツ内のインテントのギャップを検出します。 | ユーザーのインテントと既存のトピックに合わせて AI 生成の FAQ コンテンツを提案します。 | FAQ コンテンツを HTML に挿入することで、ページがより見つけやすくなり、AI 駆動型の回答の関連性を高めます。 |
| 複雑なコンテンツの簡素化 | コンテンツの最適化 | AI の理解を妨げる可能性のある複雑なテキストを含むページにフラグを付けます。 | 元の意味を維持しながら、複雑なテキストの AI 生成の簡素化バージョンを提供します。 | ページ内の複雑なセクションを書き換えて、AI の読みやすさを向上させます。 |

### その他のツール

[Adobe LLM Optimizer：web ページは引用できますか？](https://chromewebstore.google.com/detail/adobe-llm-optimizer-is-yo/jbjngahjjdgonbeinjlepfamjdmdcbcc) Chrome 拡張機能は、LLM が web ページのコンテンツにどの程度にアクセスでき、どの部分が非表示になっているかを示します。 無料のスタンドアロン診断ツールとして設計され、製品ライセンスや設定は必要ありません。

シングルクリックで、任意のサイトの機械による読みやすさを評価できます。 AI エージェントに表示される内容と人間のユーザーに表示される内容を並べて比較表示し、LLM Optimizer を使用して回復できるコンテンツの量を推定できます。 詳しくは、[AI は web サイトを読み取れるか](https://business.adobe.com/blog/introducing-the-llm-optimizer-chrome-extension) ページを参照してください。

## オポチュニティの詳細

次の節では、Edge での最適化でサポートされている各オポチュニティの追加の詳細を確認できます。

### コンテンツの可視性を回復

このオポチュニティでは、クライアントサイドレンダリングにより、AI エージェントに対して重要なコンテンツが非表示になっているページにフラグを付けます。 特定した各ページについて、AI エージェントビューに欠落しているコンテンツが正確に表示され、可視性のギャップがハイライト表示され、変更を直接適用して非表示のコンテンツを回復できます。 Edge での最適化を使用してこのオポチュニティをデプロイする際、事前にレンダリングされ、AI で最適化されたバージョンのページが LLM ユーザーエージェントに提供されるので、LLM ユーザーエージェントは JavaScript を実行せずに完全なコンテキストにアクセスできます。
これにより、ページが最初に AI エージェントに完全に表示されます。 事前にレンダリングされた HTML の上に追加の拡張機能が適用されます。

>[!IMPORTANT]
>この事前レンダリング機能は、Edge での最適化でデプロイした際、以下に示すすべての機会に自動的に適用され、ページが AI エージェントに完全に表示されます。

### LLM 対応の要約の追加

このオポチュニティでは、LLM がページコンテンツをすばやく理解することに役立つ簡潔な要約によってメリットを得ることができるページを特定します。 各ページにおいて、オポチュニティは、要約が最も必要な箇所を検出し、ページレベルやセクションレベルで AI 生成の要約を作成します。 Edge での最適化を使用してデプロイする際、AI エージェントが取得する HTML にこれらの要約が挿入され、コンテンツがより正確に記述される可能性が高まります。

### 関連する FAQ の追加

このオポチュニティでは、追加の Q&amp;A コンテンツがユーザーのインテントと AI 駆動型の検出のプロンプトにより一致する可能性があるページにフラグが付けられます。 各ページにおいて、ユーザーのインテントとページ上のコンテンツに関連付けられた AI 生成の FAQ ブロックを提案します。 Edge での最適化を使用すると、これらの FAQ が HTML に挿入され、ページがより AI 対応になり、AI の回答がユーザーの指示を直接反映する可能性が高くなります。

### 複雑なコンテンツの簡素化

このオポチュニティでは、AI の理解度を低下させる可能性のある、長く複雑な段落を含むページを検出します。 読みやすさのしきい値を超える各ページについて、元の意味を維持しながら、よりシンプルでスキャンしやすい AI 生成コンテンツを作成します。 Edge でデプロイする際、エージェントトラフィックに配信される簡素化されたコンテンツは、LLM はコンテンツをより忠実に解釈して要約するのに役立ちます。

## Edge での自動最適化

各オポチュニティについて、Edge での最適化をプレビュー、編集、デプロイ、ライブ表示、ロールバックできます。

>[!VIDEO](https://video.tv.adobe.com/v/3477983/?learn=on&enablevpops)

### プレビュー

**プレビュー**&#x200B;を使用すると、候補が公開される前にその影響を確認できます。 これにより、現在のページと、候補を適用した後に予想される AI 最適化バージョンとの違いが並べて表示されます。 このビューでは、ライブトラフィックを強化する同じ Edge での最適化ロジックを使用します。別のプレビューモードで表示されます。 これはレビュー用の読み取り専用シミュレーションなので、ライブトラフィックには影響しません。

![プレビュー](/help/assets/optimize-at-edge/preview.png)

### 編集

**編集**&#x200B;を使用すると、自動生成された候補をデプロイする前に、全体的に微調整または書き換えることができます。 候補を受け入れる代わりに、編集ワークフローを通じて完全な制御を維持します。 ビューには構造化されたエディターで提案された変更が表示され、元のインテントに一致するようにテキストを変更できます。 編集したバージョンは、デプロイすると AI エージェントに提供されます。

![編集](/help/assets/optimize-at-edge/edit.png)

### デプロイ

**デプロイ**&#x200B;を使用すると、選択した候補を公開し、最適化されたエクスペリエンスを Edge から AI エージェントに提供できます。 CDN が完全にルーティングされている場合、通常、ドメイン内のすべてのページは数分以内に新しい変更を反映して公開されます。 選択したパスにのみルーティングが設定されている場合は、許可リストに登録されたページのみが最適化されて公開されます。

![デプロイ](/help/assets/optimize-at-edge/deploy.png)

### ライブ表示

**ライブ表示**&#x200B;を使用すると、最適化がライブで実行され、エージェントトラフィックに対して期待どおりに動作していることを確認できます。この表示は、他の方法ではアクセスが困難です。 「固定候補」の下でライブページを表示できます。ここでは、AI エージェントに表示されるようにページがレンダリングされます。

![ライブ表示](/help/assets/optimize-at-edge/view-live.png)

### ロールバック

ロールバックを使用すると、以前にデプロイした最適化が安全に元に戻ります。 通常、ページの AI のみのバージョンは数分以内に以前の状態に戻されるので、必要に応じて安全に最適化を試すことができます。

![ロールバック](/help/assets/optimize-at-edge/rollback.png)

## よくある質問

Q. Edge での最適化では、どの種類の LLM をターゲットにしていますか？

ターゲットにするユーザーエージェントのリストは、オンボーディングプロセス中に定義されます。

<!--Q. What does "Edge" in Optimize at Edge mean?

In our context, "Edge" means that the optimization is applied at the CDN layer and not inside your CMS.

Q. Why does this optimization require a CDN?

The CDN is where the optimized version of the page is assembled and delivered to AI agents. We leverage the CDN to ensure your origin CMS remains unchanged. This separation lets you improve LLM visibility without altering your existing publishing workflows.-->

Q. Edge での最適化にまだオンボードしていない場合はどうなりますか？

必要な設定を完了する前に「**最適化をデプロイ**」をクリックした場合、サイトには何も適用されません。 代わりに、ポップアップダイアログが表示され、オンボーディングのサポートについては、`llmo-at-edge@adobe.com` のチームに連絡するプロンプトが表示されます。 オンボーディングが完了するまで、検出された機会と候補を探索することはできますが、ワンクリックデプロイメントワークフローは非アクティブのままになります。

Q：ソースでコンテンツを更新する際、何が発生しますか？

基になるソースページが変更されていない限り、キャッシュからページの最適化バージョンが提供されます。 ただし、**復元コンテンツの可視性** でソースが変更された場合は、システムが自動更新されるので、AI エージェントは常に最新のコンテンツを受け取ります。 これは、サイト上のコンテンツが更新されると、そのウィンドウ内で新しい最適化がトリガーされるように、低キャッシュの有効期限（TTL）設定を（分順で）使用するからです。 **LLM に対応した概要を追加する** などのコンテンツオポチュニティの場合、LLM Optimizerはソースページを監視して変更を確認します。 変更が検出された場合、エージェント表示ページと人間が表示するページの間でコンテンツがずれるのを防ぐために、最適化を一時停止して、人間によるレビューのためにフラグを立てます。
<!--As there is no universal TTL that fits every site, we can configure this TTL based on your cache invalidation rules to ensure both systems stay in sync.-->

Q. Edge での最適化は、Adobe Edge Delivery Service（EDS）を使用しているサイトのみの対象ですか？

いいえ。 Edge での最適化は、CDN に依存せず、Adobe EDS スタックにデプロイされているものだけでなく、任意のフロントエンドアーキテクチャで機能します。

Q. Edge での最適化の事前レンダリングは、従来のサーバーサイドレンダリング（SSR）とどのように異なりますか？

どちらも異なる問題を解決し、連携して動作できます。 従来の SSR では、サーバーサイドコンテンツがレンダリングされますが、後でブラウザーに読み込まれるコンテンツは含まれません。 Edge での最適化の事前レンダリングでは、JavaScript とクライアントサイドデータが読み込まれた後のページが取得され、CDN Edge で完全にアセンブリされたバージョンが生成されます。 SSR は人間のエクスペリエンスの向上に焦点を当て、Edge での最適化は LLM の web エクスペリエンスを向上させます。

質問：ドメイン内の一部の URL ではなく、すべての URL に最適化をデプロイするとどうなりますか？

明示的に最適化した URL のみが変更されます。 オポチュニティがデプロイされた URL の場合、AI エージェントは最適化されたバージョンを受け取ります。 デプロイされた機会のない URL の場合、このサービスは、変更を適用したり、最適化キャッシュレイヤーに保存したりせずに、元のページをそのままプロキシするだけです。 これにより、サイトの他の部分に影響を与えることなく、最適化を選択的にデプロイできます。
