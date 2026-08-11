---
title: Edge での最適化 - Azure フロントドア（BYOCDN）
description: LLM Optimizer の Edge での最適化に Azure Front Door BYOCDN を設定する方法について説明します。
feature: Opportunities
autotag-review: '2026-07-15T17:40:54.797Z'
TQID: 'https://experienceleague.adobe.com/fe-kultqzWQdRdcUjzfNs21UpL6m5zcoAmaQyMMv5kk'
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: d1956731-2adb-4bb7-8301-2b239254ac72id: e1b649f0-0a61-46e4-9082-64d5cb2576c6id: ef4e63f5-cb4d-462d-bf9a-1f617edf2a3aid: e0828736-236a-487b-a478-5a635455eadc
subfeature_v2: id: d23587d6-14d6-4e3f-9ee1-cc18623832e1id: e06fae5f-830b-4222-a469-b5e148d36465
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 2705cf26faea9c09817bbdcec4b4c531552df7ba
workflow-type: ht
source-wordcount: 768
ht-degree: 100%

---


# Azure Front Door（BYOCDN）

この設定では、エージェントトラフィック（AI ボットおよび LLM ユーザーエージェントからのリクエスト）を Edge での最適化バックエンドサービス（`live.edgeoptimize.net`）にルーティングします。 人間の訪問者と SEO ボットは、通常どおりオリジンから引き続き提供されます。 設定をテストするには、設定が完了したら、応答のヘッダー `x-edgeoptimize-request-id` を探します。

Azure Front Door では、Edge でカスタムコードを実行しません。ルーティングは、**ルールセット**&#x200B;と、Edge での最適化専用の&#x200B;**オリジングループ**&#x200B;を組み合わせて構成します。フェイルオーバーは、Azure Front Door の優先順位ベースのオリジングループの正常性プローブによって処理されます。

**前提条件**

Azure Front Door のルーティングルールを設定する前に、次のものを用意してください。

* Azure Front Door プロファイルへのアクセス権。
* LLM Optimizer UI から取得された Edge Optimize API キー。 手順について詳しくは、[API キーの取得](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#production-api-key)を参照してください。
* （オプション）ステージングルーティングをテストするには、[Staging API キー](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#staging-api-key-optional)を参照してください。

## 手順 1：Edge Optimize 用の配信元グループを作成する

Azure Front Door プロファイルには、配信元を指すデフォルトの配信元グループがすでに存在します。Edge Optimize 用の&#x200B;**新しい**&#x200B;配信元グループを作成します。

* **名前：** `edge-optimize-origin-group`
* **配信元（優先順位ベースのフェールオーバー）：**
  * **優先順位 1** — `live.edgeoptimize.net`（配信元ホストヘッダー：`live.edgeoptimize.net`）
  * **優先順位 2** — ドメインエンドポイント（例：`www.example.com`）。これはフェールオーバーのためです。Edge Optimize が正常に動作していない場合、リクエストはドメインエンドポイントにルーティングされ、Azure Front Door に再び送られた後、デフォルトの配信元から配信されます。
* **正常性プローブ：****有効**
  * パス：`/health/<your-domain>`（例：`/health/www.example.com`）
  * プロトコル：HTTPS
  * 間隔：225秒
* **セッションアフィニティ：****無効**
* **証明書のサブジェクト名の検証：****有効**

![2 つの優先順位ベースの配信元と正常性プローブを備えた Edge Optimize 配信元グループ](/help/assets/optimize-at-edge/azure-front-door-origin-group.png)

>[!NOTE]
>
>`edge-optimize-origin-group`配信元グループには、ポータルで&#x200B;**「関連付けられていません」**&#x200B;という警告が表示されます。これは想定どおりの動作です。配信元グループはルールセットのルートオーバーライドを通じて参照されており、ルートから直接参照されているわけではありません。

## 手順 2：ルートを構成する

通常、Azure Front Door プロファイルにはデフォルトルートが作成されています。ルールセット（手順 3）でエージェントトラフィックの配信元グループをオーバーライドするため、Edge Optimize 用に個別のルートを作成する必要はありません。

## 手順 3：ルールセットを作成する

**ルールセット** > **ルールセットの追加**&#x200B;に移動し、`EORouting`という名前を付けます。次の順序で 3 つのルールを追加します。

![ヘッダー削除ルールとボットルーティングルールを示す EORouting ルールセット](/help/assets/optimize-at-edge/azure-front-door-ruleset-routing.png)

### ルール 1：StripIncomingEOHeaders01

なりすましを防ぐため、受信した Edge Optimize ヘッダーを削除します。条件はありません。すべてのリクエストに適用されます。評価を停止：**オフ**。

**アクション**：次の各リクエストヘッダーを削除します。

* `x-edgeoptimize-url`
* `x-edgeoptimize-config`
* `x-edgeoptimize-api-key`
* `x-edgeoptimize-fetcher-key`

### ルール 2：EOGPTBotRootGET03

HTML ページパスに対するボットからのリクエストを Edge Optimize にルーティングします。評価を停止：**オン**。

**条件**（すべて一致する必要があります）：

* リクエストメソッド：**次と等しい** `GET`
* リクエストパス：**正規表現** `(^$|^.*/$|(^|.*/)[^./]+$|^.*\.html$)`（サイトのルート、`/`で終わるパス、拡張子のないページパス、および `.html` パスに一致します）
* User-Agent：**次のいずれかを含む** `chatgpt-user`、`gptbot`、`oai-searchbot`、`adobeedgeoptimize-ai`、`perplexitybot`、`perplexity-user`、`claudebot`、`claude-user`、`claude-searchbot`。文字列変換を&#x200B;**小文字に変換**&#x200B;に設定します。
* `x-edgeoptimize-monitor`：**次を含まない** `1`
* `x-edgeoptimize-request`：**次のいずれも含まない** `failover`、`1`

**アクション**：

* リクエストヘッダーを上書き `x-edgeoptimize-url` = `/{url_path}?{query_string}`
* リクエストヘッダーを上書き `x-edgeoptimize-config` = `LLMCLIENT=TRUE;`
* リクエストヘッダーを上書き `x-edgeoptimize-api-key` = `YOUR_API_KEY`
* リクエストヘッダーを上書き `x-edgeoptimize-monitor` = `1`
* ルート構成の上書き：配信元グループ → `edge-optimize-origin-group`、転送プロトコル → 受信リクエストに一致、キャッシュ → **無効**

### ルール 3：HealthProbeRewrite03

Azure Front Door の正常性プローブリクエストを、`/`ではなく `/health/<domain>` として配信元に届くように書き換えます。これにより、Azure Front Door は、配信元に専用の正常性チェックエンドポイントを用意しなくても、Edge Optimize の可用性を監視できます。評価を停止：**オン**。

![正常性プローブ書き換えルール](/help/assets/optimize-at-edge/azure-front-door-ruleset-healthprobe.png)

**条件**（すべて一致する必要があります）：

* リクエスト URL パス：**次で始まる** `/health/`
* `x-fd-healthprobe`：**次を含む** `1`

**アクション**：

* URL 書き換え：ソースパターン：`/health/`、宛先：`/`
* 応答ヘッダーの上書き`custom-origin-health` = `routed`（診断用。確認後に削除できます）
* 要求ヘッダーを追加`user-agent` = ` AdobeEdgeOptimize/1.0`（先頭に半角スペースを追加します。Azure Front Door は値をそのまま追加します）
* ルート構成の上書き：配信元グループ → `default-origin-group`、転送プロトコル → 受信リクエストに一致、キャッシュ → **無効**

## 手順 4：ルールセットをルートに関連付ける

ルートを開き、一番下の「**ルール**」セクションまでスクロールして、ドロップダウンから `EORouting` ルールセットを選択します。既存のルールセットがある場合は、**最上位へ移動**&#x200B;を使用して、`EORouting` を **#1** の位置に配置します。Edgeでの最適化ルールは、エージェントトラフィックと Edge Optimize のループバックリクエストのみを処理します。それ以外のすべてのトラフィックは影響を受けず、他のルールにそのまま渡されます。保存して、反映されるまで待ちます（約 20 分）。

## ファイアウォールルールで Edge での最適化を許可する（任意）

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
