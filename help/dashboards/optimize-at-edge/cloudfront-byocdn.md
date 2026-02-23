---
title: Edgeでの最適化 – CloudFront （BYOCDN）
description: LLM OptimizerのEdgeで最適化をおこなうために CloudFront BYOCDN を設定する方法を説明します。
feature: Opportunities
source-git-commit: 1253d0f0a58f6523699c52fbfab23028dc469c82
workflow-type: tm+mt
source-wordcount: '2228'
ht-degree: 1%

---


# CloudFront （BYOCDN）

この設定により、エージェンティックトラフィック（AI ボットや LLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 人間の訪問者と SEO ボットは、通常どおりオリジンから提供され続けます。 設定をテストするには、セットアップが完了した後、応答でヘッダー `x-edgeoptimize-request-id` を探します。

**前提条件**

CloudFront 設定を行う前に、以下を確認します。

* Web サイトを提供する既存の CloudFront 配布。
* Lambda 関数、IAM ロール、CloudFront ディストリビューション、キャッシュポリシーを作成するためのAWS IAM 権限。
* LLM Optimizerのオンボーディングプロセスを完了しました。
* LLM Optimizerへの CDN ログ転送が完了しました。
* LLM Optimizer UI から取得されたEdge最適化 API キー。

{{retrieve-byocdn-api-key}}

**手順 1:Edgeの原点の最適化を作成する**

**ナビゲーション：** AWS コンソール/CloudFront/配布版/[ 自分の配布版 ]/「オリジン」タブ

1. **原点を作成** をクリックします。

2. 接触チャネルを設定します。
   * **接触チャネルドメイン：** `live.edgeoptimize.net`
   * **名前：** `EdgeOptimize_Origin`

3. その他のフィールドはすべてデフォルト値のままにします。

4. カスタムヘッダーを追加：

   | ヘッダー | 値 |
   |--------|-------|
   | `x-edgeoptimize-api-key` | API キー |
   | `x-forwarded-host` | `www.example.com` |

   `www.example.com` を実際の web サイトドメインに置き換え、`Your API key` をAdobe担当者から提供されるEdge Optimize API キーに置き換えます。

5. **原点を作成** をクリックします。

![Cloudfront オリジンの作成 ](/help/assets/optimize-at-edge/cloudfront-origin-creation.png)

**手順 2：ビューアリクエスト関数の作成**

**ナビゲーション：** AWS コンソール/CloudFront/関数

1. **関数を作成** をクリックします。

2. 設定：
   * **名前：** `edgeoptimize-routing`
   * **Runtime:** `cloudfront-js-2.0`

3. デフォルトのコードを [viewer-request.js](https://github.com/adobe-rnd/llmo-edge-optimize-samples/blob/main/cloudfront/cloudfront-function/viewer-request.js) のコードに置き換えます。

   公開する前に、コードで次の値をカスタマイズします。

   * `YOUR_DEFAULT_ORIGIN` – 既存のデフォルトオリジンの名前に置き換えます（CloudFront/配布版/[ 配布版 ]/「オリジン」タブにあります）。
   * `TARGETED_PATHS` – すべてのHTML ページを対象とする場合は `null` に設定し、特定のパスの配列（例：`['/', '/products', '/about']`）に設定する場合はに設定します。

4. **変更を保存**/**公開関数** をクリックします。

![Cloudfront 関数の作成 ](/help/assets/optimize-at-edge/cloudfront-function-creation.png)


**手順 3：キャッシュポリシーの設定**

**ナビゲーション：** AWS コンソール/CloudFront/配布版/[ 配布版 ]/ビヘイビアー

現在ビヘイビアーに添付されているキャッシュポリシーを確認します。 動作の **編集** をクリックし、**キャッシュキーとオリジンリクエスト** セクションを見て、シナリオを特定します。

* **シナリオ A （レガシー）:** 「レガシーキャッシュ設定 **というラベルの付いたラジオボタンが選択** れています。 ポリシー名のドロップダウンはなく、代わりにインライン TTL およびヘッダー設定が表示されます。
* **シナリオ B （カスタムポリシー）:** 自分または自分のチームが作成したポリシー名で選択された **キャッシュポリシー** が表示されます（AWSが提供するポリシーではありません）。
* **シナリオ C （管理ポリシー）:** キャッシュポリシー **、`CachingOptimized`、`CachingDisabled`、`CachingOptimizedForUncompressedObjects` など、AWSが指定した名前で選択されています** これらは編集できません。

**シナリオ A：従来のキャッシュ設定**

従来のキャッシュ設定を使用している場合：

1. **キャッシュキーおよびオリジンリクエスト** の下で、**従来のキャッシュ設定** が選択されます。

2. `x-edgeoptimize-config` と `x-edgeoptimize-url` を **Headers** 許可リストに追加します。

   * ドロップダウンから **次のヘッダーを含める** を選択します。
   * `x-edgeoptimize-config` と `x-edgeoptimize-url` を追加します。
     ![Cloudfront キャッシュレガシー ](/help/assets/optimize-at-edge/cloudfront-cache-policy-legacy.png)

   「ヘッダー」ドロップダウンで既に **すべて** が選択されている場合は、この手順をスキップします。すべてのヘッダーがオリジンに自動的に転送されます。

3. **オブジェクトのキャッシュ** 設定を確認します。

   * **カスタマイズ** に設定した場合 – **最小 TTL** を `0` に設定することをお勧めします。 ただし、現在の最小 TTL が既に非常に短い場合は、変更する必要はありません。
   * **オリジンキャッシュヘッダーを使用** に設定した場合 – 変更は不要です。

4. **変更を保存** をクリックします。

**シナリオ B：カスタムキャッシュポリシーを使用した非レガシー**

ビヘイビアーで既にカスタムキャッシュポリシー（AWS管理ポリシーではなく、作成したもの）を使用している場合：

**ナビゲーション：** AWS コンソール/CloudFront/ポリシー/キャッシュ

1. 既存のポリシーをクリックします。

2. 「**編集**」をクリックします。

3. **最小 TTL** を `0` に設定することをお勧めします。 ただし、現在の最小 TTL が既に非常に短い場合は、変更する必要はありません。
   ![ キャッシュポリシー TTL 設定 ](/help/assets/optimize-at-edge/cloudfront-cache-policy-ttl.png)

4. **キャッシュキー設定**/**ヘッダー** の下に、既存のインクルージョンと共に `x-edgeoptimize-config` と `x-edgeoptimize-url` を追加します。
   ![ ポリシーヘッダーをキャッシュする ](/help/assets/optimize-at-edge/cloudfront-cache-policy-headers.png)

5. **変更を保存** をクリックします。

**シナリオ C：管理（AWS）キャッシュポリシーを使用した非レガシー**

お使いのビヘイビアーでAWS管理キャッシュポリシー（`CachingOptimized` など）を使用している場合は、それを編集することはできません。 既存の管理ポリシー設定を複製し、その上にEdge Optimize ヘッダーを追加する、新しいカスタムキャッシュポリシーを作成する必要があります。

**パート 1：現在の管理キャッシュポリシー設定をメモしておきます**

**ナビゲーション：** AWS コンソール/CloudFront/ポリシー/キャッシュ

1. 現在お使いのビヘイビアーに関連付けられている管理キャッシュポリシー（`CachingOptimized` など）を見つけてクリックします。

2. 既存の設定をすべて書き留めます。
   * 最小 TTL、最大 TTL、デフォルト TTL
   * キャッシュキーに含まれるヘッダー
   * キャッシュキーに含まれる cookie
   * キャッシュキーに含まれるクエリ文字列
   * 圧縮のサポート（Gzip、Brotli）

**パート 2：同じ設定とEdge最適化設定を使用して、新しいカスタムキャッシュポリシーを作成する**

**ナビゲーション：** AWS コンソール/CloudFront/ポリシー/キャッシュ

1. **キャッシュポリシーの作成** をクリックします。

2. **名前：** `edgeoptimize-cache`

   ![ キャッシュポリシー名 ](/help/assets/optimize-at-edge/cloudfront-cache-policy-name.png)

3. パート 1 で説明したすべての設定と、次の修正をレプリケートします。

   * **最小 TTL** を `0` に設定することをお勧めします。 ただし、現在の最小 TTL が既に非常に短い場合は、変更する必要はありません。

   ![ キャッシュポリシー TTL 設定 ](/help/assets/optimize-at-edge/cloudfront-cache-policy-ttl.png)

   * **キャッシュキー設定**/**ヘッダー** に、管理対象ポリシーにあったすべてを含め、さらに `x-edgeoptimize-config` と `x-edgeoptimize-url` を追加します。

   ![ ポリシーヘッダーをキャッシュする ](/help/assets/optimize-at-edge/cloudfront-cache-policy-headers.png)

4. 「**作成**」をクリックします。

5. 行動に戻り、新しく作成したポリシーを関連付けます。

   **ナビゲーション：** AWS コンソール/CloudFront/配布版/[ 配布版 ]/ビヘイビアー

   1. ビヘイビアーを編集します。
   2. **キャッシュキーおよびオリジンリクエスト** で、**キャッシュポリシー** を選択します。
   3. ドロップダウンから「`edgeoptimize-cache`」を選択します。
   4. **変更を保存** をクリックします。

**手順 4:Lambda@Edge関数の作成（接触チャネルリクエストと応答）**

>[!IMPORTANT]
>Lambda@Edge関数 **`us-east-1` （バージニア北部） リージョンで作成する必要があります**。 これはAWSの要件です。 関数は `us-east-1` で作成されますが、AWSは世界中のすべての CloudFront エッジ場所に自動的に関数を複製するので、ビューアに最も近いエッジ場所で実行されます。 続行する前に、AWS コンソールの `us-east-1` リージョンにいることを確認します。

**Lambda 関数の作成**

**ナビゲーション：** AWS コンソール/Lambda

1. **関数を作成** をクリックします。

2. **ゼロから作成** を選択します。

3. 設定：
   * **関数名：** `edgeoptimize-origin`
   * その他のフィールドはすべてデフォルト値のままにします。

4. **関数を作成** をクリックします。

5. コードエディターで、デフォルトのコードを [origin-request-response.js](https://github.com/adobe-rnd/llmo-edge-optimize-samples/blob/main/cloudfront/lambda/origin-request-response.js) からのコードで置き換えます。

6. 「**デプロイ**」をクリックして、コードを保存します。

7. **設定**/**権限** の下に表示される **実行ロール名** に注意してください（例：`edgeoptimize-origin-role-xxxxx`）。 これは、次の手順で必要になります。

**実行ロールの信頼ポリシーの更新**

自動作成された役割は、`lambda.amazonaws.com` のみを信頼します。 Lambda@Edgeの場合、`edgelambda.amazonaws.com` も追加する必要があります。

**ナビゲーション：** AWSコンソール/IAM/役割/[ 前のステップからの役割 ]/「信頼関係」タブ

1. **信頼ポリシーの編集** をクリックします。

2. ポリシーを [trust-policy.json](https://github.com/adobe-rnd/llmo-edge-optimize-samples/blob/main/cloudfront/lambda/trust-policy.json) のコンテンツに置き換えます。

3. **ポリシーを更新** をクリックします。

>[!WARNING]
>Lambda@Edgeの `edgelambda.amazonaws.com` サービスプリンシパルは **必須** です。 これを使用しないと、CloudFront はエッジロケーションであなたの関数を呼び出すことができません。

**CloudWatch ログの権限ポリシーの修正**

自動作成された役割には、通常のラムダ用に設定された `AWSLambdaBasicExecutionRole` ポリシーが付属していますが、このポリシーは、Lambda@Edgeに対して誤った地域とログ グループ名を持っています。 更新する必要があります。

**ナビゲーション：** AWSコンソール/IAM/役割/[ 自分の役割 ]/「権限」タブ/添付されたポリシー名（`AWSLambdaBasicExecutionRole-xxxx` など）をクリックします

1. 「**編集**」をクリックします。

2. ポリシーを [cloudwatch-policy.json](https://github.com/adobe-rnd/llmo-edge-optimize-samples/blob/main/cloudfront/lambda/cloudwatch-policy.json) のコンテンツに置き換えます。

   JSON 内で、`ACCOUNT_ID` を実際のAWS アカウント ID （AWS コンソールの右上隅にあります）に置き換え、`FUNCTION_NAME` を Lambda 関数の名前（例：`edgeoptimize-origin`）に置き換えます。

3. **変更を保存** をクリックします。

>[!WARNING]
>ARN の領域は `*` である必要があります。Lambda@Edgeは、ビューアに最も近いエッジ位置で実行されるので、ログは必ずしも `us-east-1` ードされるとは限らない、エッジ位置の領域（例：`ap-south-1`、`eu-west-1`）で CloudWatch に書き込まれます。 ロググループでは、領域のプレフィックスが付いた名前 `/aws/lambda/us-east-1.FUNCTION_NAME` を使用します。`us-east-1` は常に関数のホーム領域です。

**バージョンの公開**

1. 関数ページで、**アクション** （右上）/**新しいバージョンを公開** をクリックします。

2. 説明を追加します。

3. 「**公開する**」をクリックします。
   ![Lambda パブリッシュ ](/help/assets/optimize-at-edge/cloudfront-lambda-publish.png)

4. **Function ARN** をコピーまたはメモします。これは次の手順で必要になります。
   ![ ラムダ ARN](/help/assets/optimize-at-edge/cloudfront-lambda-arn.png)

**手順 5：関数とキャッシュポリシーを動作と関連付ける**

**ナビゲーション：** AWS コンソール/CloudFront/配布版/[ 配布版 ]/ビヘイビアー

1. ビヘイビアーを編集します。

2. 手順 3 （シナリオ C）で新しいキャッシュポリシーを作成した場合は、**キャッシュポリシー** を `edgeoptimize-cache` に設定します。
   ![ キャッシュポリシー ](/help/assets/optimize-at-edge/cloudfront-behaviour-cache.png)

3. 「**関数の関連付け**」で、次の項目を設定します。

   * **ビューアリクエスト：** `edgeoptimize-routing`
   * **接触チャネルリクエスト：** 手順 4 の（**バージョンを公開** 内の）バージョン管理された関数 ARN
   * **オリジン応答：手順 4 の** バージョン管理された関数 ARN （**バージョンの公開**）

   ![ 関数の関連付けの設定 ](/help/assets/optimize-at-edge/cloudfront-function-association.png)

4. **変更を保存** をクリックします。

**手順 6：設定をテストする**

**1. ボットトラフィックのテスト （最適化する必要があります）**

エージェント ボットユーザーエージェントと共にリクエストを送信します。 **最初のリクエスト** において、Edge Optimize は、ページの処理およびキャッシュ中に、プロキシ化された（最適化されていない）応答を返す場合があります。 これは、応答の `x-edgeoptimize-proxy: 1` ヘッダーで識別できます。

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

![ ルーティングを有効にした AI トラフィックルーティングステータス ](/help/assets/optimize-at-edge/byocdn-CDN-traffic-routed-tick.png)

**4. ログが正しくフローしていることを確認**

上記のテストリクエストを実行した後、CloudFront 関数とLambda@Edge関数の両方に対してログが書き込まれていることを確認します。

*CloudFront 関数ログ （`edgeoptimize-routing`）*

**移動：** AWS コンソール/CloudWatch/ロググループ（`us-east-1` または CloudFront 配布が設定されている地域）

1. `/aws/cloudfront/function/edgeoptimize-routing` という名前のロググループを探します。
2. 最新のログストリームを開きます。
3. エージェンティックリクエストの場合は、次のようなエントリが表示されます。
   * `Adding origin group for userAgent: chatgpt-user`
   * `Routing to Edge Optimize origin for userAgent: chatgpt-user`
4. 非 agentic リクエストの場合は、次のように表示されます。
   * `Routing to Default origin for userAgent: ...`

また、**AWS コンソール/CloudFront/機能/edgeoptimize-routing** で **指標** タブを確認して、呼び出し数とエラー率を表示することもできます。

*Lambda@Edge件のログ （`edgeoptimize-origin`）*

>[!IMPORTANT]
>Lambda@Edgeのログは、`us-east-1` ではなく、リクエストを処理した **edge ロケーションの地域** で CloudWatch に書き込まれます。 curl コマンドを実行した場所に最も近いAWS リージョンで、CloudWatch をチェックします。

**ナビゲーション：** AWS コンソール/CloudWatch/ロググループ（正しいリージョンにいることを確認します）

1. `/aws/lambda/us-east-1.edgeoptimize-origin` という名前のロググループを探します。
2. 最新のログストリームを開きます。
3. エージェンティックリクエストの場合は、次のようなエントリが表示されます。
   * `Calling Edge Optimize Origin for agentic requests` — プライマリパス
   * `Calling Default Origin in case of failover for agentic requests` — フェイルオーバーパス
   * `Failover Triggered for agentic requests` — オリジン応答のフェイルオーバー検出

ロググループが存在しない場合は、手順 4 で IAM 権限が正しく更新されたことを確認します。 また、近くのAWS リージョンも確認してください。リクエストを提供したエッジの場所は、期待したものとは異なる場合があります。

**トラブルシューティング**

| 問題 | 考えられる原因 | 解決策 |
|-------|----------------|----------|
| 代理店のリクエストに対応する `x-edgeoptimize-request-id` ールがありません | 接触チャネルグループがEdge Optimize にルーティングされていない | CloudFront のファンクションコード（手順 2）で、`YOUR_DEFAULT_ORIGIN` が正しく置き換えられていることを確認します。 |
| アジェンティックリクエストの 403 エラー | API キーが無効または見つかりません | Edge Optimize の接触チャネルカスタムヘッダー（手順 1）で `x-edgeoptimize-api-key` ヘッダーの値を確認します。 |
| Lambda@Edgeの CloudWatch ログが見つかりません | 間違った IAM 権限 | CloudWatch ログの権限ポリシーが更新されたことを確認します（手順 4）。 注意：Lambda@Edge ログは、必ずしも `us-east-1` ージでなくても、リクエストを提供したエッジ領域に表示されます。 |
| キャッシュが `cache-control: no-store` に従っていない | 最小 TTL が高すぎる可能性があります | キャッシュポリシーで、最小 TTL を `0` に設定します（手順 3）。 最小 TTL がすでに非常に短い場合は、問題ない可能性があります。 |
| 通常の（非エージェンティック）トラフィックがセットアップ後に壊れる | キャッシュポリシーの設定ミス | 新しいキャッシュポリシー（シナリオ C）を作成した場合は、元の管理ポリシーからすべての設定がレプリケートされていることを確認します。 |

**Edgeでの最適化の無効化と再有効化**

Lambda@Edge関数（`edgeoptimize-origin`）は、CloudFront 動作のオリジンリクエストおよびオリジン応答イベントに関連付けられています。 なぜなら、Lambda@Edgeの停止は、人間と Agentic の両方のその行動を通過するすべてのリクエストでインラインで実行されるので、Agentic リクエストだけでなく、すべてのライブトラフィックに影響を与えます。 Lambda@Edgeの停止が検出された場合は、関数の関連付けを直ちに削除し、通常のトラフィックフローをデフォルトオリジンに復元します。

**Lambda@Edgeの停止を検出する方法**

* **AWS サービス正常性ダッシュボード** — **AWS CloudFront](https://health.aws.amazon.com/health/status) または** Amazon Lambda} に影響を与えるアクティブなインシデントについては **[AWS サービス正常性ダッシュボード** を確認してください。 ここで報告されるグローバルまたは地域的な停止は、問題が設定ではなくAWS インフラストラクチャ側にあることを確認する最も迅速な方法です。
* **Lambda@Edge errors** — **AWS コンソール/CloudFront/モニタリング/[ 配布版]** に移動します。 「**Lambda@Edge エラー**」タブを開き、「**実行エラー**」グラフで実行エラーを確認します。 これらの値が大きい場合、Lambda@Edgeがダウンしている可能性があります。

**Lambda@Edge関数の分離**

**ナビゲーション：** AWS コンソール/CloudFront/配布版/[ 配布版 ]/ビヘイビアー

1. 動作の **編集** をクリックします。

2. 「**関数の関連付け**」セクションまでスクロールします。

3. 次の関連付けを「**関連付けなし**」に設定します。

   | イベント | をに変更 |
   |---|---|
   | ビューアリクエスト | 関連付けなし |
   | 接触チャネルリクエスト | 関連付けなし |
   | 接触チャネル応答 | 関連付けなし |

   ![ 関数の関連付けの設定 ](/help/assets/optimize-at-edge/cloudfront-no-function-association.png)

4. **変更を保存** をクリックします。

5. CloudFront 配布がデプロイを完了するまで待ちます。 ステータスが **デプロイ** から最終変更日（通常、数分以内）に変更されます。

デプロイが完了すると、すべてのトラフィックルートはデフォルトオリジンに直接ルーティングされます。 設定は削除されません。Lambda 関数とその関連付けはいつでも復元できます。

**Lambda@Edge関数の再接続**

**ナビゲーション：** AWS コンソール/CloudFront/配布版/[ 配布版 ]/ビヘイビアー

1. 動作の **編集** をクリックします。

2. 「**関数の関連付け**」セクションまでスクロールします。

3. 関連付けを復元します。

   | イベント | をに設定 |
   |---|---|
   | ビューアリクエスト | `edgeoptimize-routing` （CloudFront 関数） |
   | 接触チャネルリクエスト | 手順 4 のバージョン管理された Lambda ARN |
   | 接触チャネル応答 | 手順 4 のバージョン管理された Lambda ARN |

   手順 4 で示したバージョン付き **関数 ARN** を使用します（**バージョンの公開**）。

   ![ 関数の関連付けの設定 ](/help/assets/optimize-at-edge/cloudfront-function-association.png)

4. **変更を保存** をクリックします。

5. 配布がデプロイを完了するのを待ってから、Agentic リクエストが手順 6 で説明したように `x-edgeoptimize-request-id` ヘッダーを返すことを確認します。

{{return-to-overview}}
