---
title: Edgeで最適化 – CloudFront （BYOCDN）
description: LLM OptimizerのEdgeでCloudFront BYOCDN for Optimizeを設定する方法について説明します。
feature: Opportunities
source-git-commit: 13d2f4bbd1f9d3886f89f80df0e76093f2afdf13
workflow-type: tm+mt
source-wordcount: '2207'
ht-degree: 1%

---


# CloudFront （BYOCDN）

この設定により、エージェント型トラフィック（AI ボットおよびLLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 通常どおり、人間の訪問者とSEO ボットは、元のページから引き続き提供されます。 設定をテストするには、設定が完了した後、応答でヘッダー`x-edgeoptimize-request-id`を探します。

**前提条件**

CloudFront設定を設定する前に、次のことを確認してください。

* Web サイトを提供する既存のCloudFront ディストリビューション。
* Lambda関数、IAM ロール、CloudFront ディストリビューション、およびキャッシュポリシーを作成するためのAWS IAM権限。
* LLM Optimizer UIから取得したEdge Optimize API キー。 手順については、[API キーの取得](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#production-api-key)を参照してください。
* （オプション）ステージング ルーティングをテストするには、[&#x200B; ステージング API キー](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#staging-api-key-optional)を参照してください。

**手順1: Edge Optimize Originの作成**

**ナビゲーション：** AWS コンソール > CloudFront > ディストリビューション > [ ディストリビューション ] > 「配信元」タブ

1. 「**原点を作成**」をクリックします。

2. オリジンを設定します。
   * **オリジンドメイン：** `live.edgeoptimize.net`
   * **名前：** `EdgeOptimize_Origin`

3. その他のすべてのフィールドをデフォルト値のままにします。

4. カスタムヘッダーを追加：

   | ヘッダー | 値 |
   |--------|-------|
   | `x-edgeoptimize-api-key` | あなたのAPI キー |
   | `x-forwarded-host` | `www.example.com` |

   `www.example.com`を実際のweb サイトドメインに、そして`Your API key`をAdobe担当者から提供されたEdge Optimize API キーに置き換えます。

5. 「**原点を作成**」をクリックします。

![Cloudfront オリジンの作成](/help/assets/optimize-at-edge/cloudfront-origin-creation.png)

**手順2: ビューアリクエスト関数を作成**

**ナビゲーション：** AWS コンソール > CloudFront >関数

1. 「**関数を作成**」をクリックします。

2. 設定：
   * **名前：** `edgeoptimize-routing`
   * **実行時間：** `cloudfront-js-2.0`

3. デフォルトコードを[viewer-request.js](https://github.com/adobe-rnd/llmo-edge-optimize-samples/blob/main/cloudfront/cloudfront-function/viewer-request.js)のコードに置き換えます。

   公開する前に、コードで次の値をカスタマイズします。

   * `YOUR_DEFAULT_ORIGIN` – 既存のデフォルトのオリジンの名前に置き換えます（CloudFront/ディストリビューション/[ ディストリビューション ]/オリジン タブにあります）。
   * `TARGETED_PATHS` – すべてのHTML ページをターゲットにするか、特定のパスの配列（例：`['/', '/products', '/about']`）に設定するには、`null`に設定します。

4. **変更を保存** > **関数を公開**&#x200B;をクリックします。

![Cloudfront関数の作成](/help/assets/optimize-at-edge/cloudfront-function-creation.png)


**手順3: キャッシュポリシーの設定**

**ナビゲーション：** AWS コンソール > CloudFront > ディストリビューション > [ ディストリビューション ] >動作

現在ビヘイビアーにアタッチされているキャッシュポリシーを確認します。 行動の&#x200B;**編集**&#x200B;をクリックし、**キャッシュキーとオリジンリクエスト** セクションを確認して、シナリオを特定します。

* **シナリオ A （レガシー）:** 「**レガシーキャッシュ設定**」というラベルのラジオボタンが選択されています。 ポリシー名のドロップダウンはなく、インライン TTLとヘッダーの設定が表示されます。
* **シナリオ B （カスタムポリシー）:**&#x200B;自分またはチームが作成したポリシー名で&#x200B;**キャッシュポリシー**&#x200B;が選択されています（AWSが提供するポリシーではありません）。
* **シナリオ C （管理ポリシー）:** `CachingOptimized`、`CachingDisabled`、`CachingOptimizedForUncompressedObjects`など、AWSが提供する名前で&#x200B;**キャッシュポリシー**&#x200B;が選択されています。これらは編集できません。

**シナリオ A：従来のキャッシュ設定**

ビヘイビアーで従来のキャッシュ設定を使用している場合：

1. **キャッシュキーとオリジンリクエスト**&#x200B;の下に、**レガシーキャッシュ設定**&#x200B;が選択されています。

2. `x-edgeoptimize-config`と`x-edgeoptimize-url`を&#x200B;**Headers**&#x200B;許可リストに追加します。

   * ドロップダウンから「**次のヘッダーを含める**」を選択します。
   * `x-edgeoptimize-config`と`x-edgeoptimize-url`を追加します。
     ![Cloudfront キャッシュ レガシー](/help/assets/optimize-at-edge/cloudfront-cache-policy-legacy.png)

   「ヘッダー」ドロップダウンで「**すべて**」が既に選択されている場合は、この手順をスキップしてください。すべてのヘッダーが自動的にオリジンに転送されます。

3. 「**オブジェクトキャッシュ**」設定を確認します。

   * **カスタマイズ**&#x200B;に設定されている場合は、**最小TTL**&#x200B;を`0`に設定することをお勧めします。 ただし、現在の最小TTLがすでに非常に短い場合は、変更する必要がない可能性があります。
   * **オリジンキャッシュヘッダーを使用**&#x200B;に設定した場合、変更は必要ありません。

4. 「**変更を保存**」をクリックします。

**シナリオ B：カスタムキャッシュポリシーを持つレガシー以外**

ビヘイビアーで既にカスタムキャッシュポリシーを使用している場合（作成したカスタムキャッシュポリシーではなく、AWSが管理するポリシーを使用している場合）:

**ナビゲーション：** AWS コンソール / CloudFront / ポリシー/ キャッシュ

1. 既存のポリシーをクリックします。

2. 「**編集**」をクリックします。

3. **最小TTL**&#x200B;を`0`に設定することをお勧めします。 ただし、現在の最小TTLがすでに非常に短い場合は、変更する必要がない可能性があります。
   ![&#x200B; キャッシュポリシーのTTL設定](/help/assets/optimize-at-edge/cloudfront-cache-policy-ttl.png)

4. **キャッシュキー設定** > **ヘッダー**&#x200B;の下に、既存のインクルージョンと共に`x-edgeoptimize-config`と`x-edgeoptimize-url`を追加します。
   ![&#x200B; キャッシュポリシーヘッダー](/help/assets/optimize-at-edge/cloudfront-cache-policy-headers.png)

5. 「**変更を保存**」をクリックします。

**シナリオ C：管理対象（AWS） キャッシュ ポリシーを持つレガシー以外**

ビヘイビアーでAWS マネージドキャッシュポリシー（例：`CachingOptimized`）を使用している場合、それを編集することはできません。 既存のマネージドポリシー設定を複製し、上部にEdge Optimize ヘッダーを追加する新しいカスタムキャッシュポリシーを作成する必要があります。

**パート 1：現在のマネージド キャッシュ ポリシー設定を書き留めます**

**ナビゲーション：** AWS コンソール / CloudFront / ポリシー/ キャッシュ

1. 現在ビヘイビアーに添付されているマネージドキャッシュポリシー（例：`CachingOptimized`）を検索してクリックします。

2. 既存のすべての設定をメモします。
   * 最小TTL、最大TTL、デフォルト TTL
   * キャッシュキーに含まれるヘッダー
   * キャッシュキーに含まれるCookie
   * キャッシュキーに含まれるクエリ文字列
   * 圧縮サポート（Gzip、Brotli）

**パート 2：同じ設定で新しいカスタム キャッシュ ポリシーを作成する+ Edge Optimize config**

**ナビゲーション：** AWS コンソール / CloudFront / ポリシー/ キャッシュ

1. 「**キャッシュポリシーの作成**」をクリックします。

2. **名前：** `edgeoptimize-cache`

   ![&#x200B; キャッシュポリシー名](/help/assets/optimize-at-edge/cloudfront-cache-policy-name.png)

3. パート 1に記載されているすべての設定を、次の変更を加えてレプリケートします。

   * **最小TTL**&#x200B;を`0`に設定することをお勧めします。 ただし、現在の最小TTLがすでに非常に短い場合は、変更する必要がない可能性があります。

   ![&#x200B; キャッシュポリシーのTTL設定](/help/assets/optimize-at-edge/cloudfront-cache-policy-ttl.png)

   * **キャッシュキー設定** > **ヘッダー**&#x200B;の下で、管理ポリシーが持つすべてを含め、さらに`x-edgeoptimize-config`と`x-edgeoptimize-url`を追加します。

   ![&#x200B; キャッシュポリシーヘッダー](/help/assets/optimize-at-edge/cloudfront-cache-policy-headers.png)

4. 「**作成**」をクリックします。

5. ビヘイビアーに戻り、新しく作成したポリシーを関連付けます。

   **ナビゲーション：** AWS コンソール > CloudFront > ディストリビューション > [ ディストリビューション ] >動作

   1. 行動の編集：
   2. **キャッシュキーとオリジンリクエスト**&#x200B;で、**キャッシュポリシー**&#x200B;を選択します。
   3. ドロップダウンから「`edgeoptimize-cache`」を選択します。
   4. 「**変更を保存**」をクリックします。

**ステップ 4: Lambda@Edge関数を作成します（オリジンのリクエストと応答）**

>[!IMPORTANT]
>Lambda@Edge関数&#x200B;**は、`us-east-1` （バージニア北部）地域**&#x200B;で作成する必要があります。 これはAWSの要件です。 関数が`us-east-1`で作成されたとしても、AWSは自動的に世界中のすべてのCloudFront エッジロケーションにレプリケートするため、ビューアに最も近いエッジロケーションで実行されます。 続行する前に、AWS Consoleの`us-east-1` リージョンにいることを確認してください。

**Lambda関数を作成**

**ナビゲーション：** AWS コンソール > Lambda

1. 「**関数を作成**」をクリックします。

2. **作成者をゼロから選択**&#x200B;します。

3. 設定：
   * **関数名：** `edgeoptimize-origin`
   * その他のすべてのフィールドをデフォルト値のままにします。

4. 「**関数を作成**」をクリックします。

5. コードエディターで、デフォルトコードを[origin-request-response.js](https://github.com/adobe-rnd/llmo-edge-optimize-samples/blob/main/cloudfront/lambda/origin-request-response.js)のコードに置き換えます。

6. 「**デプロイ**」をクリックして、コードを保存します。

7. **設定**/**権限** （例：`edgeoptimize-origin-role-xxxxx`）の下に表示されている&#x200B;**実行ロール名**&#x200B;に注意してください。 次の手順で必要になります。

**実行ロールの信頼ポリシーを更新する**

自動作成された役割は`lambda.amazonaws.com`のみを信頼します。 Lambda@Edgeの場合は、`edgelambda.amazonaws.com`も追加する必要があります。

**ナビゲーション：** AWS コンソール > IAM > ロール > [前の手順のロール ] > 「信頼関係」タブ

1. 「**信頼ポリシーを編集**」をクリックします。

2. ポリシーを[trust-policy.json](https://github.com/adobe-rnd/llmo-edge-optimize-samples/blob/main/cloudfront/lambda/trust-policy.json)のコンテンツに置き換えます。

3. 「**ポリシーを更新**」をクリックします。

>[!WARNING]
>`edgelambda.amazonaws.com` サービス プリンシパルは、Lambda@Edgeに&#x200B;**必須**&#x200B;です。 これを使用しない場合、CloudFrontはエッジの場所で関数を呼び出すことはできません。

**CloudWatch Logs権限ポリシーの修正**

自動作成された役割には、通常のLambda用に設定された`AWSLambdaBasicExecutionRole` ポリシーが付属しています。このポリシーには、Lambda@Edgeのリージョンとロググループ名が間違っています。 それを更新する必要があります。

**ナビゲーション：** AWS コンソール > IAM > ロール > [自分のロール ] >権限タブ >添付されたポリシー名をクリックします（例：`AWSLambdaBasicExecutionRole-xxxx`）

1. 「**編集**」をクリックします。

2. ポリシーを[cloudwatch-policy.json](https://github.com/adobe-rnd/llmo-edge-optimize-samples/blob/main/cloudfront/lambda/cloudwatch-policy.json)のコンテンツに置き換えます。

   JSONで、`ACCOUNT_ID`を実際のAWS アカウント ID （AWS コンソールの右上隅）に、`FUNCTION_NAME`をLambda関数の名前（例：`edgeoptimize-origin`）に置き換えます。

3. 「**変更を保存**」をクリックします。

>[!WARNING]
>ARNの領域は`*`である必要があります – Lambda@Edgeはビューアに最も近いエッジの場所で実行されるので、ログはエッジの場所の領域（例：`ap-south-1`、`eu-west-1`）でCloudWatchに書き込まれます。必ずしも`us-east-1`ではありません。 ロググループは、領域接頭辞が付いた名前を使用します：`/aws/lambda/us-east-1.FUNCTION_NAME`。`us-east-1`は常に関数のホーム領域です。

**バージョンを公開**

1. 関数ページで、**アクション** （右上）/**新しいバージョンを公開**&#x200B;をクリックします。

2. 説明を追加します。

3. 「**公開する**」をクリックします。
   ![Lambda パブリッシュ &#x200B;](/help/assets/optimize-at-edge/cloudfront-lambda-publish.png)

4. **関数ARN**&#x200B;をコピーまたはメモします。次の手順で必要になります。
   ![Lambda ARN](/help/assets/optimize-at-edge/cloudfront-lambda-arn.png)

**手順5：関数とキャッシュ ポリシーをビヘイビアーに関連付ける**

**ナビゲーション：** AWS コンソール > CloudFront > ディストリビューション > [ ディストリビューション ] >動作

1. 行動の編集：

2. 手順3 （シナリオ C）で新しいキャッシュポリシーを作成した場合は、**キャッシュポリシー**&#x200B;を`edgeoptimize-cache`に設定します。
   ![&#x200B; キャッシュポリシー](/help/assets/optimize-at-edge/cloudfront-behaviour-cache.png)

3. **関数の関連付け**&#x200B;で、次を設定します。

   * **閲覧者の要求：** `edgeoptimize-routing`
   * **オリジンリクエスト：** ステップ 4のバージョン付き関数ARN （**バージョンの公開**&#x200B;で）
   * **発信元の応答：** ステップ 4のバージョン付き関数ARN （**バージョンの公開**&#x200B;で）

   ![関数の関連付け設定](/help/assets/optimize-at-edge/cloudfront-function-association.png)

4. 「**変更を保存**」をクリックします。

**ファイアウォール ルールを使用してEdgeで最適化を許可する（オプション）**

{{waf-allowlist-setup}}

**手順6：設定のテスト**

**1. ボットトラフィックのテスト （最適化する必要があります）**

エージェント型ボットユーザーエージェントでリクエストを送信します。 **最初のリクエスト**&#x200B;で、Edge Optimizeがページを処理およびキャッシュする際に、プロキシ化された（最適化されていない）応答を返す場合があります。 応答の`x-edgeoptimize-proxy: 1` ヘッダーでこれを識別できます。

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

**4. ログが正しく流れていることを確認する**

上記のテストリクエストを実行した後、CloudFront関数とLambda@Edge関数の両方にログが書き込まれていることを確認します。

*CloudFront関数ログ （`edgeoptimize-routing`）*

**ナビゲーション：** AWS コンソール / CloudWatch / ロググループ（`us-east-1`またはCloudFront ディストリビューションが設定されているリージョン）

1. `/aws/cloudfront/function/edgeoptimize-routing`という名前のロググループを探します。
2. 最新のログストリームを開きます。
3. エージェント型リクエストの場合は、次のようなエントリが表示されます。
   * `Adding origin group for userAgent: chatgpt-user`
   * `Routing to Edge Optimize origin for userAgent: chatgpt-user`
4. エージェント以外のリクエストの場合は、次を参照してください。
   * `Routing to Default origin for userAgent: ...`

**AWS コンソール/CloudFront/関数/edgeoptimize-routing**&#x200B;の「**指標**」タブで、呼び出し数とエラー率を確認することもできます。

*Lambda@Edge ログ （`edgeoptimize-origin`）*

>[!IMPORTANT]
>Lambda@Edge ログは、`us-east-1`ではなく、リクエストを処理したエッジの場所&#x200B;**の**&#x200B;領域でCloudWatchに書き込まれます。 curl コマンドを実行した場所に最も近いAWS リージョンのCloudWatchを確認します。

**ナビゲーション：** AWS コンソール/CloudWatch/ロググループ（正しいリージョンに属していることを確認してください）

1. `/aws/lambda/us-east-1.edgeoptimize-origin`という名前のロググループを探します。
2. 最新のログストリームを開きます。
3. エージェント型リクエストの場合は、次のようなエントリが表示されます。
   * `Calling Edge Optimize Origin for agentic requests` — プライマリパス
   * `Calling Default Origin in case of failover for agentic requests` — フェールオーバーパス
   * `Failover Triggered for agentic requests` — origin-response フェールオーバー検出

ロググループが存在しない場合は、手順4でIAM権限が正しく更新されていることを確認します。 また、近くにある他のAWS リージョンも確認してください。リクエストを提供したエッジの場所は、想定したものとは異なる場合があります。

**トラブルシューティング**

| 問題 | 考えられる原因 | 解決策 |
|-------|----------------|----------|
| エージェント型リクエストに対する応答に`x-edgeoptimize-request-id`がありません | オリジン グループがEdge Optimizeにルーティングされない | CloudFront関数コードで`YOUR_DEFAULT_ORIGIN`が正しく置き換えられたことを確認します（手順2）。 |
| エージェント型リクエストで403 エラーが発生する | API キーが無効または見つかりません | Edge Optimize origin カスタムヘッダーの`x-edgeoptimize-api-key` ヘッダー値を確認します（手順1）。 |
| Lambda@EdgeのCloudWatch ログが見つかりません | 間違ったIAM権限 | CloudWatch Logs権限ポリシーが更新されたことを確認します（手順4）。 メモ：Lambda@Edge ログは、必ずしも`us-east-1`ではなく、リクエストを提供したエッジ領域に表示されます。 |
| キャッシュが`cache-control: no-store`を尊重していません | 最小値TTLが高すぎる可能性があります | キャッシュポリシーで最小TTLを`0`に設定します（手順3）。 最小値のTTLがすでに非常に短い場合は、この問題が発生しない可能性があります。 |
| セットアップ後に正常な（エージェント以外の）トラフィックが破損する | キャッシュポリシーの設定ミス | 新しいキャッシュポリシー（シナリオ C）を作成した場合は、元のマネージドポリシーのすべての設定をレプリケートしてください。 |

**EdgeでのOptimizeの無効化と再有効化**

Lambda@Edge関数（`edgeoptimize-origin`）は、CloudFront ビヘイビアーのオリジン要求イベントおよびオリジン応答イベントに関連付けられます。 Lambda@Edgeの停止は、その動作を通過するあらゆるリクエスト（人間とエージェントの両方）に対してインラインで実行されるので、エージェントのリクエストだけでなく、すべてのライブトラフィックに影響します。 Lambda@Edgeの停止を検出した場合は、すぐに関数の関連付けを削除して、通常のトラフィックフローをデフォルトのオリジンに戻します。

**Lambda@Edgeの障害を検出する方法**

* **AWS Service Health Dashboard** — **AWS CloudFront**&#x200B;または&#x200B;**AWS Lambda**&#x200B;に影響を与えるアクティブなインシデントについては、[Amazon Service Health Dashboard](https://health.aws.amazon.com/health/status)を確認してください。 ここで報告されるグローバルまたはリージョンの障害は、問題が設定ではなくAWS インフラストラクチャ側にあることを確認する最速の方法です。
* **Lambda@Edge errors** — **AWS コンソール / CloudFront / モニタリング / [ ディストリビューション]**&#x200B;に移動します。 「**Lambda@Edge errors**」タブを開き、**実行エラー** グラフで実行エラーを確認します。 これらの値が高い場合は、Lambda@Edgeがダウンしている可能性があります。

**Lambda@Edge関数のデタッチ**

**ナビゲーション：** AWS コンソール > CloudFront > ディストリビューション > [ ディストリビューション ] >動作

1. ビヘイビアーで「**編集**」をクリックします。

2. **関数の関連付け** セクションまでスクロールします。

3. 次の関連付けを&#x200B;**関連付けなし**&#x200B;に設定します。

   | イベント | 変更先 |
   |---|---|
   | Viewer リクエスト | 関連付けなし |
   | オリジンリクエスト | 関連付けなし |
   | オリジンレスポンス | 関連付けなし |

   ![関数の関連付け設定](/help/assets/optimize-at-edge/cloudfront-no-function-association.png)

4. 「**変更を保存**」をクリックします。

5. CloudFront ディストリビューションのデプロイが完了するのを待ちます。 ステータスが&#x200B;**デプロイ**&#x200B;から最終変更日に変更されます（通常は数分以内）。

デプロイが完了すると、すべてのトラフィックルートがデフォルトのオリジンに直接送信されます。 設定は削除されません。Lambda関数とその関連付けはいつでも復元できます。

**Lambda@Edge関数を再アタッチしています**

**ナビゲーション：** AWS コンソール > CloudFront > ディストリビューション > [ ディストリビューション ] >動作

1. ビヘイビアーで「**編集**」をクリックします。

2. **関数の関連付け** セクションまでスクロールします。

3. 関連付けを復元：

   | イベント | に設定 |
   |---|---|
   | Viewer リクエスト | `edgeoptimize-routing` （CloudFront関数） |
   | オリジンリクエスト | 手順4のバージョン管理されたLambda ARN |
   | オリジンレスポンス | 手順4のバージョン管理されたLambda ARN |

   手順4でメモしたバージョン管理された&#x200B;**Function ARN**&#x200B;を使用します（**バージョンの公開**&#x200B;で）。

   ![関数の関連付け設定](/help/assets/optimize-at-edge/cloudfront-function-association.png)

4. 「**変更を保存**」をクリックします。

5. ディストリビューションのデプロイが完了するのを待ってから、エージェンティック要求が手順6で説明されているように`x-edgeoptimize-request-id` ヘッダーを返すことを確認します。

{{return-to-overview}}
