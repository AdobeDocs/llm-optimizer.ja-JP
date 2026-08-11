---
title: Edge での最適化 - CloudFront（BYOCDN）
description: LLM Optimizer の Edge での最適化に CloudFront BYOCDN を設定する方法について説明します。
feature: Opportunities
autotag-review: '2026-07-15T17:46:25.674Z'
TQID: 'https://experienceleague.adobe.com/yIEUTzlnvOX-WBf276KQcAN8sGYDpZNVibJt024VMWU'
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: d1956731-2adb-4bb7-8301-2b239254ac72id: e1b649f0-0a61-46e4-9082-64d5cb2576c6id: ef4e63f5-cb4d-462d-bf9a-1f617edf2a3aid: e0828736-236a-487b-a478-5a635455eadc
subfeature_v2: id: d23587d6-14d6-4e3f-9ee1-cc18623832e1id: e06fae5f-830b-4222-a469-b5e148d36465
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: c1579802-ddd4-4214-8a91-97b2066abe11id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: e36ee407933e2d3d56cadf1c9517f23f24d41d91
workflow-type: tm+mt
source-wordcount: 2360
ht-degree: 95%

---


# CloudFront（BYOCDN）

この設定では、エージェントトラフィック（AI ボットおよび LLM ユーザーエージェントからのリクエスト）を Edge での最適化バックエンドサービス（`live.edgeoptimize.net`）にルーティングします。 人間の訪問者と SEO ボットは、通常どおりオリジンから引き続き提供されます。 設定をテストするには、設定が完了したら、応答のヘッダー `x-edgeoptimize-request-id` を探します。

**前提条件**

CloudFront 設定を指定する前に、次を確認します。

* Web サイトを提供する既存の CloudFront 配布。
* Lambda 関数、IAM 役割、CloudFront 配布およびキャッシュポリシーを作成する AWS IAM 権限。
* LLM Optimizer UI から取得された Edge Optimize API キー。 手順について詳しくは、[API キーの取得](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#production-api-key)を参照してください。
* （オプション）ステージングルーティングをテストするには、[Staging API キー](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#staging-api-key-optional)を参照してください。

## 手順1:Edge Optimize Originの作成

**ナビゲーション：** AWS コンソール／CloudFront／配布／[配布]／オリジンタブ

1. 「**オリジンを作成**」をクリックします。

2. オリジンを設定します。
   * **オリジンドメイン：** `live.edgeoptimize.net`
   * **名前：** `EdgeOptimize_Origin`

3. その他のフィールドはすべてデフォルト値のままにします。

4. カスタムヘッダーを追加します。

   | ヘッダー | 値 |
   |--------|-------|
   | `x-edgeoptimize-api-key` | API キー |
   | `x-forwarded-host` | `www.example.com` |
   | `x-edgeoptimize-fetcher-key` | Fetcher キー（WAF の許可リスト登録を行う場合にのみ必須） |

   `www.example.com` を実際の web サイトのドメインに、`Your API key` をアドビ担当者から提供された Edge Optimize API キーに置き換えます。

5. 「**オリジンを作成**」をクリックします。

![Cloudfront オリジンの作成](/help/assets/optimize-at-edge/cloudfront-origin-creation.png)

## 手順2：ビューアリクエスト関数の作成

**ナビゲーション：** AWS コンソール／CloudFront／関数

1. 「**関数を作成**」をクリックします。

2. 設定：
   * **名前：** `edgeoptimize-routing`
   * **ランタイム：** `cloudfront-js-2.0`

3. デフォルトのコードを [viewer-request.js](https://github.com/adobe/llmo-code-samples/blob/main/optimize-at-edge/cloudfront/cloudfront-function/viewer-request.js) のコードに置き換えます。

   公開する前に、コード内の次の値をカスタマイズします。

   * `YOUR_DEFAULT_ORIGIN` - 既存のデフォルトのオリジンの名前に置き換えます（CloudFront／配分／[配分]／オリジンタブで確認）。
   * `TARGETED_PATHS` - すべての HTML ページをターゲットにするには `null` に設定するか、特定のパスの配列（例：`['/', '/products', '/about']`）に設定します。

4. **変更を保存**／**関数を公開**&#x200B;をクリックします。

![Cloudfront 関数の作成](/help/assets/optimize-at-edge/cloudfront-function-creation.png)


## 手順3：キャッシュポリシーの設定

**ナビゲーション：** AWS コンソール／CloudFront／配分／[配分]／動作

現在、動作にアタッチされているキャッシュポリシーを確認します。 動作の「**編集**」をクリックし、「**キャッシュキーとオリジンリクエスト**」セクションを確認して、シナリオを特定します。

* **シナリオ A （レガシー）：****レガシーキャッシュ設定**&#x200B;というラベルのラジオボタンが選択されている状態が表示されます。 ポリシー名のドロップダウンはなく、代わりにインライン TTL とヘッダー設定が表示されます。
* **シナリオ B （カスタムポリシー）：****キャッシュポリシー**&#x200B;が選択されている状態が表示され、そのポリシー名は、自分または自分のチームが作成した名前（AWS が提供したポリシーではない）です。
* **シナリオ C （管理ポリシー）：****キャッシュポリシー**&#x200B;が選択されている状態が表示され、その AWS が提供した名前は `CachingOptimized`、`CachingDisabled` または `CachingOptimizedForUncompressedObjects` などですが、これらは編集できません。

### シナリオ A：従来のキャッシュ設定

動作でレガシーキャッシュ設定を使用している場合は、次の手順に従います。

1. **キャッシュキーとオリジンリクエスト**&#x200B;の下に、「**レガシーキャッシュ設定**」が選択されている状態が表示されます。

2. **ヘッダー**&#x200B;許可リストに `x-edgeoptimize-config` と `x-edgeoptimize-url` を追加します。

   * ドロップダウンから「**次のヘッダーを含める**」を選択します。
   * `x-edgeoptimize-config` と `x-edgeoptimize-url` を追加します。
     ![Cloudfront キャッシュレガシー](/help/assets/optimize-at-edge/cloudfront-cache-policy-legacy.png)

   ヘッダードロップダウンで既に「**すべて**」が選択されている場合は、この手順をスキップします。すべてのヘッダーは自動的にオリジンに転送されます。

3. 「**オブジェクトキャッシュ**」設定を確認します。

   * **カスタマイズ**&#x200B;に設定されている場合 - **最小 TTL** を `0` に設定することをお勧めします。 ただし、現在の最小 TTL が既に非常に短い場合、変更する必要はありません。
   * **オリジンキャッシュヘッダーを使用**&#x200B;に設定した場合 - 変更は必要ありません。

4. 「**変更を保存**」をクリックします。

### シナリオ B：カスタムキャッシュポリシーを使用したレガシー以外

動作で既にカスタムキャッシュポリシー（AWS が管理するポリシーではなく、作成したポリシー）を使用している場合は、次の手順に従います。

**ナビゲーション：** AWS コンソール／CloudFront／ポリシー／キャッシュ

1. 既存のポリシーをクリックします。

2. 「**編集**」をクリックします。

3. **最小 TTL** を `0` に設定することをお勧めします。 ただし、現在の最小 TTL が既に非常に短い場合、変更する必要はありません。
   ![キャッシュポリシー TTL 設定](/help/assets/optimize-at-edge/cloudfront-cache-policy-ttl.png)

4. **キャッシュキー設定**／**ヘッダー**&#x200B;の下に、既存のインクルージョンと共に、`x-edgeoptimize-config` と `x-edgeoptimize-url` を追加します。
   ![キャッシュポリシーヘッダー](/help/assets/optimize-at-edge/cloudfront-cache-policy-headers.png)

5. 「**変更を保存**」をクリックします。

### シナリオ C：管理対象（AWS）キャッシュポリシーを使用したレガシー以外

動作で AWS が管理するキャッシュポリシー（例：`CachingOptimized`）を使用している場合、編集できません。 既存の管理するポリシー設定をレプリケートし、上部に Edge での最適化ヘッダーを追加する新しいカスタムキャッシュポリシーを作成する必要があります。

**第 1 部：現在の管理するキャッシュポリシー設定をメモ**

**ナビゲーション：** AWS コンソール／CloudFront／ポリシー／キャッシュ

1. 現在、動作にアタッチされている管理するキャッシュポリシー（例：`CachingOptimized`）を検索してクリックします。

2. 既存のすべての設定をメモします。
   * 最小 TTL、最大 TTL、デフォルトの TTL
   * キャッシュキーに含まれるヘッダー
   * キャッシュキーに含まれる Cookie
   * キャッシュキーに含まれるクエリ文字列
   * 圧縮サポート（Gzip、Brotli）

**第 2 部：同じ設定 + Edge での最適化設定で新しいカスタムキャッシュポリシーを作成**

**ナビゲーション：** AWS コンソール／CloudFront／ポリシー／キャッシュ

1. 「**キャッシュポリシーを作成**」をクリックします。

2. **名前：** `edgeoptimize-cache`

   ![キャッシュポリシー名](/help/assets/optimize-at-edge/cloudfront-cache-policy-name.png)

3. 第 1 部に記載されているすべての設定を、次の変更を行ってレプリケートします。

   * **最小 TTL** を `0` に設定することをお勧めします。 ただし、現在の最小 TTL が既に非常に短い場合、変更する必要はありません。

   ![キャッシュポリシー TTL 設定](/help/assets/optimize-at-edge/cloudfront-cache-policy-ttl.png)

   * **キャッシュキー設定**／**ヘッダー**&#x200B;の下に、管理するポリシーが持つすべてを含めて、`x-edgeoptimize-config` と x-`x-edgeoptimize-url` を追加します。

   ![キャッシュポリシーヘッダー](/help/assets/optimize-at-edge/cloudfront-cache-policy-headers.png)

4. 「**作成**」をクリックします。

5. 動作に戻り、新しく作成したポリシーを関連付けます。

   **ナビゲーション：** AWS コンソール／CloudFront／配分／[配分]／動作

   1. 動作を編集します。
   2. **キャッシュキーとオリジンリクエスト**&#x200B;の下にある「**キャッシュポリシー**」を選択します。
   3. ドロップダウンから `edgeoptimize-cache` を選択します。
   4. 「**変更を保存**」をクリックします。

## ステップ 4: Lambda@Edge関数を作成する（オリジンのリクエストとレスポンス）

>[!IMPORTANT]
>Lambda@Edge 関数は、**`us-east-1`（バージニア北部）地域**&#x200B;で作成する必要があります。 これは AWS の要件です。 関数を `us-east-1` で作成したとしても、AWS は世界中のすべての CloudFront Edge の場所に自動的にレプリケートするので、ビューアに最も近い Edge の場所で実行されます。 先に進む前に、AWS コンソールで `us-east-1` 地域にいることを確認してください。

### Lambda関数の作成

**ナビゲーション：** AWS コンソール／Lambda

1. 「**関数を作成**」をクリックします。

2. 「**ゼロから作成者**」を選択します。

3. 設定：
   * **関数名：** `edgeoptimize-origin`
   * その他のフィールドはすべてデフォルト値のままにします。

4. 「**関数を作成**」をクリックします。

5. コードエディターで、デフォルトのコードを [origin-request-response.js](https://github.com/adobe/llmo-code-samples/blob/main/optimize-at-edge/cloudfront/lambda/origin-request-response.js) のコードに置き換えます。

6. 「**デプロイ**」をクリックして、コードを保存します。

7. **設定**／**権限**&#x200B;の下に表示される&#x200B;**実行役割名**&#x200B;をメモします（例：`edgeoptimize-origin-role-xxxxx`）。 これは、次の手順で必要になります。

### 実行ロールの信頼ポリシーの更新

自動作成された役割は、`lambda.amazonaws.com` のみを信頼します。 Lambda@Edge の場合は、`edgelambda.amazonaws.com` も追加する必要があります。

**ナビゲーション／** AWS コンソール／IAM／役割／[前の手順での役割]／信頼関係タブ

1. 「**信頼ポリシーを編集**」をクリックします。

2. ポリシーを [trust-policy.json](https://github.com/adobe/llmo-code-samples/blob/main/optimize-at-edge/cloudfront/lambda/trust-policy.json) の内容に置き換えます。

3. 「**ポリシーを更新**」をクリックします。

>[!WARNING]
>Lambda@Edge には、`edgelambda.amazonaws.com` サービスプリンシパルが&#x200B;**必須**&#x200B;です。 これがないと、CloudFront は Edge の場所で関数を呼び出すことができません。

### CloudWatch Logs権限ポリシーの修正

通常の Lambda 用に設定された `AWSLambdaBasicExecutionRole` ポリシーが含まれますが、Lambda@Edge に対して地域とロググループ名が間違っています。 これを更新する必要があります。

**ナビゲーション：** AWSコンソール／IAM／役割／[役割]／権限タブ／アタッチされているポリシー名をクリックします（例：`AWSLambdaBasicExecutionRole-xxxx`）。

1. 「**編集**」をクリックします。

2. ポリシーを [cloudwatch-policy.json](https://github.com/adobe/llmo-code-samples/blob/main/optimize-at-edge/cloudfront/lambda/cloudwatch-policy.json) の内容に置き換えます。

   JSON では、`ACCOUNT_ID` を実際の AWS アカウント ID（AWS コンソールの右上隅に表示）に、`FUNCTION_NAME` を Lambda 関数の名前（例：`edgeoptimize-origin`）に置き換えます。

3. 「**変更を保存**」をクリックします。

>[!WARNING]
>ARN の地域は `*` にする必要があります - Lambda@Edge はビューアに最も近い Edge の場所で実行されるので、ログは必ずしも `us-east-1` ではなく、Edge の場所の地域（例：`ap-south-1`、`eu-west-1`）の CloudWatch に書き込まれます。 ロググループは、地域名を接頭辞とした名前（`/aws/lambda/us-east-1.FUNCTION_NAME`）を使用します。ここで、`us-east-1` は常にその関数のホーム地域です。

### CloudWatch ログリンクの修正

デフォルトでは、Lambda コンソールの **CloudWatch ログを表示**&#x200B;ショートカットは `us-east-1` リージョンの `/aws/lambda/FUNCTION_NAME` にリンクされますが、これは Lambda@Edge に対して間違ったロググループです。 リンクが正しいパスを指すように、カスタムロググループを設定します。

**ナビゲーション：** AWS コンソール > Lambda > [お使いの関数] > 設定 > 監視と操作ツール

1. 「**編集**」をクリックします。

2. **CloudWatch ロググループ**&#x200B;で、「**カスタム**」を選択します。

3. カスタムロググループ名を `/aws/lambda/us-east-1.edgeoptimize-origin` に設定します。

4. **権限**&#x200B;で、「**必要な権限を追加**」チェックボックスを&#x200B;**オフ**&#x200B;のままにします。

   ![Lambda カスタムロググループ設定](/help/assets/optimize-at-edge/cloudfront-lambda-custom-log-group.png)

5. 「**保存**」をクリックします。

>[!NOTE]
>この修正を行った後でも、**CloudWatch ログを表示**&#x200B;リンクは正しいロググループ名を開きますが、間違った地域に存在する場合はデータが表示されないことがあります。 Lambda@Edge ログは、`us-east-1` ではなく、リクエスト（例：`eu-west-1`、`ap-south-1`）を提供した Edge の地域で書き込まれます。 引き続き、CloudWatch の正しい地域に切り替えて、ログを確認する必要があります。

### バージョンの公開

1. 関数ページで、**アクション**（右上）／**新しいバージョンを公開**&#x200B;をクリックします。

2. 説明を追加します。

3. 「**公開する**」をクリックします。
   ![Lambda の公開](/help/assets/optimize-at-edge/cloudfront-lambda-publish.png)

4. **関数 ARN** をコピーまたはメモします。これは、次の手順で必要になります。
   ![Lambda ARN](/help/assets/optimize-at-edge/cloudfront-lambda-arn.png)

## 手順5：関数とキャッシュポリシーを動作に関連付ける

**ナビゲーション：** AWS コンソール／CloudFront／配分／[配分]／動作

1. 動作を編集します。

2. 手順 3（シナリオ C）で新しいキャッシュポリシーを作成した場合は、**キャッシュポリシー** を `edgeoptimize-cache` に設定します。
   ![キャッシュポリシー](/help/assets/optimize-at-edge/cloudfront-behaviour-cache.png)

3. **関数の関連付け**&#x200B;の下で、次を設定します。

   * **ビューアリクエスト：** `edgeoptimize-routing`
   * **オリジンリクエスト：**&#x200B;手順 4（**バージョンの公開**）からバージョン管理された関数 ARN
   * **オリジン応答：**&#x200B;手順 4（**バージョンの公開**）からバージョン管理された関数 ARN

   ![関数の関連付け設定](/help/assets/optimize-at-edge/cloudfront-function-association.png)

4. 「**変更を保存**」をクリックします。

## ファイアウォールルールで Edge での最適化を許可する（任意）

{{waf-allowlist-setup}}

## 手順6：設定のテスト

**1. ボットトラフィックをテスト（最適化する必要があります）**

エージェントボットのユーザーエージェントを使用してリクエストを送信します。 **最初のリクエスト**&#x200B;時に、Edge での最適化では、ページを処理およびキャッシュする間、プロキシされた（最適化されていない）応答を返す場合があります。 これは、応答に含まれる `x-edgeoptimize-proxy: 1` ヘッダーで特定できます。

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
| `x-edgeoptimize-fo` | フェイルオーバーが発生した場合のみ存在（値：`1`） | 不在 |

{{verify-routing-status-in-ui}}

**4. ログが正しくフローされていることを確認**

上記のテストリクエストを実行した後、CloudFront 関数と Lambda@Edge 関数の両方についてログが書き込まれていることを確認します。

*CloudFront 関数ログ（`edgeoptimize-routing`）*

**ナビゲーション：** AWS コンソール／CloudWatch／ロググループ（`us-east-1` またはCloudFront 配分が設定されている地域）

1. `/aws/cloudfront/function/edgeoptimize-routing` という名前のロググループを探します。
2. 最新のログストリームを開きます。
3. エージェント型リクエストの場合、次のようなエントリが表示されます。
   * `Adding origin group for userAgent: chatgpt-user`
   * `Routing to Edge Optimize origin for userAgent: chatgpt-user`
4. エージェント型以外のリクエストの場合、次を参照してください。
   * `Routing to Default origin for userAgent: ...`

また、**AWS コンソール／CloudFront／関数／edgeoptimize-routing** の下にある「**指標**」タブを確認すると、呼び出し回数とエラー率を確認できます。

*Lambda@Edge ログ（`edgeoptimize-origin`）*

>[!IMPORTANT]
>Lambda@Edge ログは、`us-east-1` 地域ではなく、リクエストを提供した **Edge の場所の地域**&#x200B;の CloudWatch に書き込まれます。 curl コマンドを実行した場所に最も近い AWS 地域の CloudWatch を確認します。

**ナビゲーション：** AWS コンソール／CloudWatch／ロググループ（正しい地域に属していることを確認します）

1. `/aws/lambda/us-east-1.edgeoptimize-origin` という名前のロググループを探します。
2. 最新のログストリームを開きます。
3. エージェント型リクエストの場合、次のようなエントリが表示されます。
   * `Calling Edge Optimize Origin for agentic requests` - プライマリパス
   * `Calling Default Origin in case of failover for agentic requests` — フェイルオーバーパス
   * `Failover Triggered for agentic requests` - origin-response フェールオーバー検出

ロググループが存在しない場合は、手順 4 で IAM 権限が正しく更新されたことを確認します。 また、近くにある他の AWS 地域も確認します。リクエストを提供した Edge の場所は、想定していたものと異なる場合があります。

## トラブルシューティング

| 問題 | 考えられる原因 | 解決策 |
|-------|----------------|----------|
| エージェント型リクエストに対する応答に `x-edgeoptimize-request-id` がない | オリジングループが Edge での最適化にルーティングしない | CloudFront 関数コード内で `YOUR_DEFAULT_ORIGIN` が正しく置き換えられていることを確認します（手順 2）。 |
| エージェント型リクエストで 403 エラーが発生する | API キーが無効または欠落している | Edge での最適化オリジンのカスタムヘッダーで `x-edgeoptimize-api-key` ヘッダーの値を確認します（手順 1）。 |
| Lambda@Edge の CloudWatch ログが見つからない | 間違った IAM 権限 | CloudWatch Logs 権限ポリシーが更新されたことを確認します（手順 4）。 メモ：Lambda@Edge ログは、必ずしも `us-east-1` ではなく、リクエストを提供した Edge の地域に表示されます。 |
| キャッシュが `cache-control: no-store` を適用していない | 最小 TTL が高すぎる場合がある | キャッシュポリシーで最小 TTL を `0` に設定します（手順 3）。 最小 TTL が既に非常に短い場合は、これが問題ではないことがあります。 |
| 設定後に通常の（エージェント型以外の）トラフィックが破損する | キャッシュポリシーの設定ミス | 新しいキャッシュポリシーを作成した場合（シナリオC）、元の管理するポリシーからすべての設定をレプリケートしたことを確認します。 |

## EdgeでのOptimizeの無効化と再有効化

Lambda@Edge 関数（`edgeoptimize-origin`）は、CloudFront 動作のオリジンリクエストおよびオリジン応答イベントに関連付けられています。 Lambda@Edge は、その動作を通過するすべてのリクエスト（人間とエージェントの両方）に対してインラインで実行されるので、Lambda@Edge の停止は、エージェント型リクエストだけでなく、すべてのライブトラフィックに影響を与えます。 Lambda@Edge の停止を検出した場合は、すぐに関数の関連付けを削除して、デフォルトのオリジンへの通常のトラフィックフローを復元します。

### Lambda@Edgeの障害を検出する方法

* **AWS サービスヘルスダッシュボード** - [AWS サービスヘルスダッシュボード](https://health.aws.amazon.com/health/status)で、**Amazon CloudFront** または **AWS Lambda** に影響を与えているアクティブなインシデントがないか確認します。 ここで報告されたグローバルまたは地域的な停止は、問題が設定ではなく AWS インフラストラクチャ側にあることを最も迅速に確認する方法です。
* **Lambda@Edge エラー** - **AWS コンソール／CloudFront／監視／[配分]**&#x200B;に移動します。 「**Lambda@Edge エラー**」タブを開き、**実行エラー**&#x200B;グラフで実行エラーを確認します。 エラー数が高い場合は、Lambda@Edge がダウンしている可能性があります。

### Lambda@Edge関数のデタッチ

**ナビゲーション：** AWS コンソール／CloudFront／配分／[配分]／動作

1. 動作で「**編集**」をクリックします。

2. 「**関数の関連付け**」セクションまでスクロールします。

3. 次の関連付けを&#x200B;**関連付けなし**&#x200B;に設定します。

   | イベント | 変更先 |
   |---|---|
   | ビューアリクエスト | 関連付けなし |
   | オリジンリクエスト | 関連付けなし |
   | オリジン応答 | 関連付けなし |

   ![関数の関連付け設定](/help/assets/optimize-at-edge/cloudfront-no-function-association.png)

4. 「**変更を保存**」をクリックします。

5. CloudFront 配分のデプロイが完了するのを待機します。 ステータスは&#x200B;**デプロイ中**&#x200B;から最終変更日に変更されます（通常は数分以内）。

デプロイ後、すべてのトラフィックがデフォルトのオリジンに直接ルーティングされます。 設定は削除されません。Lambda 関数とその関連付けはいつでも復元できます。

### Lambda@Edge関数を再アタッチする

**ナビゲーション：** AWS コンソール／CloudFront／配分／[配分]／動作

1. 動作で「**編集**」をクリックします。

2. 「**関数の関連付け**」セクションまでスクロールします。

3. 関連付けを復元します。

   | イベント | 設定先 |
   |---|---|
   | ビューアリクエスト | `edgeoptimize-routing`（CloudFront 関数） |
   | オリジンリクエスト | 手順 4 からのバージョン管理された Lambda ARN |
   | オリジン応答 | 手順 4 からのバージョン管理された Lambda ARN |

   手順 4（**バージョンの公開**）でメモしたバージョン管理された **関数 ARN** を使用します。

   ![関数の関連付け設定](/help/assets/optimize-at-edge/cloudfront-function-association.png)

4. 「**変更を保存**」をクリックします。

5. 配分のデプロイが完了するまで待機し、手順 6 の説明に従って、エージェント型リクエストが `x-edgeoptimize-request-id` ヘッダーを返すことを確認します。

{{return-to-overview}}
