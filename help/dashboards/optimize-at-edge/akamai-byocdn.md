---
title: Edgeでの最適化 – Akamai （BYOCDN）
description: LLM OptimizerのEdgeでAkamai BYOCDN for Optimizeを設定する方法について説明します。
feature: Opportunities
source-git-commit: f2a652761acbea7ca5b8e8740c1dbd0132e42f7f
workflow-type: tm+mt
source-wordcount: '849'
ht-degree: 9%

---


# Akamai （BYOCDN）

この設定により、エージェント型トラフィック（AI ボットおよびLLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 通常どおり、人間の訪問者とSEO ボットは、元のページから引き続き提供されます。 設定をテストするには、設定が完了した後、応答でヘッダー`x-edgeoptimize-request-id`を探します。

**前提条件**

Akamai プロパティマネージャールールを設定する前に、次のことを確認してください。

* ドメインのAkamai Property Managerへのアクセス。
* LLM Optimizer オンボーディングプロセスを完了しました。
* LLM OptimizerへのCDN ログの転送が完了しました。
* LLM Optimizer UIから取得したEdge Optimize API キー。
* （オプション）最初にステージングホスト名でルーティングをテストする場合は、ステージング Edge Optimize API キー。

{{retrieve-byocdn-api-key}}

{{retrieve-staging-edge-optimize-api-key}}

**設定**

次のAkamai Property Manager ルールは、エージェント型HTML ページトラフィックをEdge Optimizeにルーティングします。 設定には、次の手順が含まれます。

**1. ルーティング条件の設定（User-AgentとHTMLのトラフィックの一致）**

次のユーザーエージェントのルーティングを設定します。

```
 *AdobeEdgeOptimize-AI*
 *ChatGPT-User*
 *GPTBot*
 *OAI-SearchBot*
 *PerplexityBot*
 *Perplexity-User*
```

>[!NOTE]
>
>Edgeで最適化のルーティングルールをエージェント型HTML ページトラフィックにのみ適用します。 一般的な設定では、**File Extension**&#x200B;などのリクエストサイドの条件を使用して、拡張機能のないページ URLに`html`と`EMPTY_STRING`を一致させることができます。 サイトが他のURL パターンからHTMLを提供している場合、またはAPI エンドポイントなどの拡張なし非ページルートを含んでいる場合は、パスベースの条件を追加してルールを絞り込みます。

![ルーティング条件を設定](/help/assets/optimize-at-edge/akamai-step1-routing.png)

**2. オリジンと SSL の動作を設定**

オリジンを`live.edgeoptimize.net`に設定し、SANを`*.edgeoptimize.net`に一致

>[!NOTE]
>
>Edgeで最適化ルールを追加した後にプロパティのアクティベーションが失敗した場合は、ルールでデフォルトのルールとは異なるOrigin Server SSL検証モードが使用されているかどうかを確認します。 その場合は、デフォルトのルールに一致するように、「Edgeで最適化」ルールを更新します。 例えば、デフォルトのルールで&#x200B;**プラットフォーム設定**&#x200B;を使用している場合は、ここでも&#x200B;**プラットフォーム設定**&#x200B;を使用します。 必要な設定を使用できない場合は、Akamai サポートにお問い合わせください。

![オリジンと SSL の動作を設定](/help/assets/optimize-at-edge/akamai-step2-origin.png)

**3. キャッシュキー変数を設定**

キャッシュキー変数`PMUSER_EDGE_OPTIMIZE_CACHE_KEY`を`LLMCLIENT=TRUE;X_FORWARDED_HOST={{builtin.AK_HOST}}`に設定します

![キャッシュキー変数を設定](/help/assets/optimize-at-edge/akamai-step3-cachekey.png)

**4. キャッシュルール**

![キャッシュルール](/help/assets/optimize-at-edge/akamai-step4-rules.png)

**5. 受信リクエストヘッダーを変更**

次の受信リクエストヘッダーを設定します。
LLMOから取得したAPI キーへの`x-edgeoptimize-api-key`
`x-edgeoptimize-config` コピー先： `LLMCLIENT=TRUE;`
`x-edgeoptimize-url` ～ `{{builtin.AK_URL}}`

![受信リクエストヘッダーを変更](/help/assets/optimize-at-edge/akamai-step5-request.png)

**6. 受信応答ヘッダーを変更**

![受信応答ヘッダーを変更](/help/assets/optimize-at-edge/akamai-step6-response.png)

**7. キャッシュ ID の変更**

![キャッシュ ID の変更](/help/assets/optimize-at-edge/akamai-step7-cacheid.png)

**8. 送信リクエストヘッダーを変更**

`x-forwarded-host` ヘッダーを`{{builtin.AK_HOST}}`に設定

![送信リクエストヘッダーを変更](/help/assets/optimize-at-edge/akamai-step8-outgoing-request.png)

**9. サイトフェイルオーバー**

サイト フェールオーバー設定には、フェールオーバー動作（メインのエッジ最適化ルーティング ルール内で設定）と個別のフェールオーバーテストヘッダールールの2つの部分があります。

**9a。 サイト フェールオーバー動作（メインのエッジで最適化ルーティング ルール内）**

メインのルーティングルール内で、サイトフェールオーバー動作と高度なXML スニペットを次のように設定します。

>[!IMPORTANT]
>
>この手順のXML スニペットには、**詳細**&#x200B;動作が必要です。 一部のAkamai環境では、この動作はセルフサービス編集では使用できません。 **Advanced** オプションが表示されない場合は、Akamai アカウントチームまたはAkamai サポートに連絡して、必要な設定を有効にしてください。

![サイトフェイルオーバー](/help/assets/optimize-at-edge/akamai-step9-failover.png)

高度なXMLを使用して、値`fo`のリクエストヘッダー`x-edgeoptimize-request`を追加します。

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

**9b. フェールオーバーテスト ヘッダー規則（兄弟ルール）**

>[!IMPORTANT]
>
>**EdgeOptimize Failover - Test Header** ルールを、ルーティングルールの&#x200B;**兄弟** （同じレベル）として作成します（**not**）。 Akamai プロパティマネージャールールツリーでは、階層は次のようになります。
>
>```
>▼ Parent Rule
>   ▶ Optimize at Edge Routing     ← routing rule
>       EdgeOptimize Failover - Test Header       ← sibling, same level
>```
>
>これにより、フェールオーバーテストヘッダールールは、1つだけでなく&#x200B;**all** ルーティングルールに対しても評価されます。
>
>また、**Edge ルーティングで最適化** ルールが、同じリクエストのオリジン、キャッシュ動作、またはキャッシュ IDを変更する後で一致するルールによって上書きされないようにします。 別の一致するルールがこれらの動作をリセットする場合、Edgeで最適化のルーティングまたはキャッシュが期待どおりに動作しない可能性があります。

リクエストヘッダー`x-edgeoptimize-request`の値が`fo`の場合、送信レスポンスヘッダー`x-edgeoptimize-fo`を`true`に設定します。

![フェイルオーバールール](/help/assets/optimize-at-edge/akamai-step9-failover-rules.png)

サイトフェールオーバーは、Edge Optimizeが`4XX`または`5XX` エラーを返した場合、リクエストがデフォルトのオリジンに自動的にルーティングされ、エンドユーザーが応答を受け取るようにします。

| シナリオ | 動作 |
| --- | --- |
| Edge Optimizeは`2XX`を返します | 最適化された応答がクライアントに提供されます。 |
| Edge Optimizeは`4XX`または`5XX`を返します | リクエストはデフォルトのオリジンに戻されます。 |

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

LLM Optimizerのステージングホスト名とステージング API キーを使用する場合は、ルールの&#x200B;**ステージング** キーを使用して、**ステージング** Akamai プロパティに同じルーティングパターンをデプロイします。 次に、ステージングホストのボットトラフィックを確認します。

```
curl -svo /dev/null https://staging.example.com/page.html \
  --header "user-agent: chatgpt-user"
```

`https://staging.example.com/page.html`を実際のステージング URLとパスに置き換えます。 応答が成功すると、`x-edgeoptimize-request-id` ヘッダーが含まれます。

{{verify-routing-status-in-ui}}

{{return-to-overview}}
