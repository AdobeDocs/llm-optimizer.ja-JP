---
title: Edgeで最適化 – Azure フロントドア （BYOCDN）
description: LLM OptimizerのEdgeでAzure Front Door BYOCDN for Optimizeを設定する方法について説明します。
feature: Opportunities
autotag-review: '2026-07-15T17:40:54.797Z'
TQID: 'https://experienceleague.adobe.com/fe-kultqzWQdRdcUjzfNs21UpL6m5zcoAmaQyMMv5kk'
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: d1956731-2adb-4bb7-8301-2b239254ac72id: e1b649f0-0a61-46e4-9082-64d5cb2576c6id: ef4e63f5-cb4d-462d-bf9a-1f617edf2a3aid: e0828736-236a-487b-a478-5a635455eadc
subfeature_v2: id: d23587d6-14d6-4e3f-9ee1-cc18623832e1id: e06fae5f-830b-4222-a469-b5e148d36465
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 2705cf26faea9c09817bbdcec4b4c531552df7ba
workflow-type: tm+mt
source-wordcount: 768
ht-degree: 24%

---


# Azure正面玄関（BYOCDN）

この設定では、エージェントトラフィック（AI ボットおよび LLM ユーザーエージェントからのリクエスト）を Edge での最適化バックエンドサービス（`live.edgeoptimize.net`）にルーティングします。 人間の訪問者と SEO ボットは、通常どおりオリジンから引き続き提供されます。 設定をテストするには、設定が完了したら、応答のヘッダー `x-edgeoptimize-request-id` を探します。

Azure Front Doorでは、エッジでカスタムコードを実行しません。 ルーティングは、**ルールセット**&#x200B;と専用の&#x200B;**オリジングループ**&#x200B;を使用してEdge Optimize用に設定されます。 フェールオーバーは、Azure Front Doorの優先度ベースのオリジングループ ヘルスプローブによって処理されます。

**前提条件**

Azure フロントドアのルーティングルールを設定する前に、次のことを確認してください。

* Azure Front Doorのプロフィールにアクセスします。
* LLM Optimizer UI から取得された Edge Optimize API キー。 手順について詳しくは、[API キーの取得](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#production-api-key)を参照してください。
* （オプション）ステージングルーティングをテストするには、[Staging API キー](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#staging-api-key-optional)を参照してください。

## 手順1:Edge Optimizeのオリジンのグループを作成する

Azure Front Door プロファイルには、既に、オリジンを指すデフォルトのオリジン グループがあります。 Edge Optimize用に&#x200B;**new** オリジン グループを作成します。

* **名前：** `edge-optimize-origin-group`
* **発信元（優先ベースのフェールオーバー）:**
   * **優先度1** — `live.edgeoptimize.net` （オリジン ホスト ヘッダー：`live.edgeoptimize.net`）
   * **優先度2** — ドメイン エンドポイント （例：`www.example.com`）。 これはフェールオーバー用です。Edge Optimizeが正常でない場合、リクエストはドメインにルーティングされ、Azure フロントドアに再エントリし、デフォルトのオリジンから提供されます。
* **正常性プローブ：** **有効**
   * パス：`/health/<your-domain>` （例：`/health/www.example.com`）
   * プロトコル：HTTPS
   * 間隔：225秒
* **セッションの親和性：** **無効**
* **証明書の件名の検証：** **有効**

![Edge Optimize origin groupと2つの優先度ベースのオリジンとヘルスプローブ ](/help/assets/optimize-at-edge/azure-front-door-origin-group.png)

>[!NOTE]
>
>`edge-optimize-origin-group`の発信元グループに、ポータルに&#x200B;**「関連付けられていません」**&#x200B;の警告が表示されます。 これは想定されています。ルートから直接ではなく、ルールセットのルートオーバーライドを使用して参照されます。

## 手順2：ルートの設定

デフォルトのルートは、通常、Azureのフロントドアのプロファイルで作成されます。 ルールセット（ステップ 3）は、エージェント型トラフィックのオリジングループを上書きするため、Edge Optimizeに個別のルートは必要ありません。

## 手順3：ルールセットの作成

**ルールセット** > **ルールセット**&#x200B;に移動し、`EORouting`という名前を付けます。 この順序で3つのルールを追加します。

ヘッダーストリッピングとボットルーティングのルールを示す![EORouting ルールセット ](/help/assets/optimize-at-edge/azure-front-door-ruleset-routing.png)

### ルール 1: StripIncomingEOHeaders01

Edge Optimizeの受信ヘッダーを削除して、スプーフィングを防止します。 条件なし – すべてのリクエストに適用されます。 評価を停止：**オフ**。

**アクション** – 各件のリクエストヘッダーを削除：

* `x-edgeoptimize-url`
* `x-edgeoptimize-config`
* `x-edgeoptimize-api-key`
* `x-edgeoptimize-fetcher-key`

### ルール 2: EOGPTBotRootGET03

HTML ページパスのボットリクエストをEdge Optimizeにルーティングします。 評価を停止：**上**。

**条件** （すべて一致する必要があります）:

* リクエストメソッド：**次と等しい** `GET`
* リクエストパス：**RegEx** `(^$|^.*/$|(^|.*/)[^./]+$|^.*\.html$)` （サイトルート、`/`で終わるパス、拡張機能のないページパス、`.html`個のパスに一致）
* User-Agent: **いずれかの** `chatgpt-user`、`gptbot`、`oai-searchbot`、`adobeedgeoptimize-ai`、`perplexitybot`、`perplexity-user`、`claudebot`、`claude-user`、`claude-searchbot`が含まれます。 文字列変換を&#x200B;**小文字**&#x200B;に設定します。
* `x-edgeoptimize-monitor`: **含まれていません** `1`
* `x-edgeoptimize-request`: **** `failover`、`1`のいずれも含まれていません

**アクション**:

* リクエストヘッダーの上書き`x-edgeoptimize-url` = `/{url_path}?{query_string}`
* リクエストヘッダーの上書き`x-edgeoptimize-config` = `LLMCLIENT=TRUE;`
* リクエストヘッダーの上書き`x-edgeoptimize-api-key` = `YOUR_API_KEY`
* リクエストヘッダーの上書き`x-edgeoptimize-monitor` = `1`
* ルート設定の上書き：発信元グループ → `edge-optimize-origin-group`、転送プロトコル →着信要求の一致、キャッシュ → **無効**

### ルール 3: HealthProbeRewrite03

Azure Front Door ヘルスプローブのリクエストを書き換えて、`/health/<domain>`ではなく`/`としてオリジンに到達させます。 これにより、Azure Front Doorは、オリジンに専用のヘルスエンドポイントを必要とせずに、Edge Optimizeの可用性を監視できます。 評価を停止：**上**。

![ ヘルスプローブの書き換えルール ](/help/assets/optimize-at-edge/azure-front-door-ruleset-healthprobe.png)

**条件** （すべて一致する必要があります）:

* リクエスト URL パス：**が** `/health/`で始まります
* `x-fd-healthprobe`: **次を含む** `1`

**アクション**:

* URL書き換え – Source パターン：`/health/`、宛先：`/`
* 応答ヘッダーの上書き`custom-origin-health` = `routed` （診断 – 検証後に削除できます）
* Request header append `user-agent` = ` AdobeEdgeOptimize/1.0` （先頭のスペースを追加 – Azure Front Doorは値をそのまま追加します）
* ルート設定の上書き：発信元グループ → `default-origin-group`、転送プロトコル →着信要求の一致、キャッシュ → **無効**

## 手順4：ルールセットをルートに関連付ける

ルートを開き、下部の「**ルール**」セクションまでスクロールし、ドロップダウンから「`EORouting`」ルールセットを選択します。 既存のルールセットがある場合は、**上に移動**&#x200B;を使用して、`EORouting`を&#x200B;**#1**&#x200B;に配置します。 Edgeでの最適化ルールでは、エージェント型トラフィックとEdge Optimizeのループバックリクエストのみが処理されます。他のトラフィックは、他のルールに影響を与えずに通過します。 保存して反映を待ちます（約20分）。

## ファイアウォールルールによるEdgeでの最適化を許可する（オプション）

{{waf-allowlist-setup}}

## 設定の検証

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

{{verify-routing-status-in-ui}}

{{return-to-overview}}
