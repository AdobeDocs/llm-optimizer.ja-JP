---
title: ログ転送 - CloudFront
description: LLM Optimizer でのエージェントトラフィックのデータ収集において、CloudFront からアドビの S3 バケットに CDN ログを転送する方法について説明します。
feature: Agentic Traffic
autotag-review: '2026-07-15T17:47:22.372Z'
TQID: 'https://experienceleague.adobe.com/0aPeInYmcNRZLHUdABG2cEpT-dXb6GhEMoNUqMLMusY'
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: d1956731-2adb-4bb7-8301-2b239254ac72id: e0828736-236a-487b-a478-5a635455eadc
subfeature_v2: id: d23587d6-14d6-4e3f-9ee1-cc18623832e1id: e69d5a42-0217-4ca5-9396-a9a826a170daid: e06fae5f-830b-4222-a469-b5e148d36465
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: d3cdead0-685a-4489-9250-4bb709942f66id: e1e0219c-f879-479f-8427-888ed2a6e9c2id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 2705cf26faea9c09817bbdcec4b4c531552df7ba
workflow-type: tm+mt
source-wordcount: 466
ht-degree: 100%

---


# ログ転送：CloudFront {#log-forwarding-cloudfront}

このページでは、エージェントトラフィックのデータ収集を行うために、CloudFront の CDN ログをアドビの S3 バケットに転送する方法について説明します。 LLM Optimizer にオンボードするには、LLM Optimizer CDN 設定ページを使用します。 オンボーディングプロセスが完了したら、このページに記載されている手順に従って、CloudFront ダッシュボードでログ転送を設定します。

## 手順 1：LLM Optimizer でオンボード {#step-1}

LLM Optimizer ページ [https://llmo.now/](https://llmo.now/) で、次の手順に従います。

1. **顧客設定ダッシュボード**&#x200B;に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN 設定**」タブをクリックします。

   ![「CDN 設定」タブ](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)

1. 「**開始**」をクリックします。

   <!-- ![Onboard CDN button](/help/overview/assets/log-forwarding/common/onboard-cdn-button.png)-->

1. 「**AI トラフィックインサイトをアクティブ化**」の横にある「**設定**」をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)

1. **AWS アカウント** ID を入力します。

   ![AWS アカウント ID](/help/overview/assets/log-forwarding/cloudfront/cloudfront-aws-account.png)

1. 「**CloudFront（BYOCDN）**」を選択します。

   ![CloudFront を選択](/help/overview/assets/log-forwarding/cloudfront/cloudfront-select.png)

1. 「**オンボード**」をクリックします。

   ![「オンボード」ボタン](/help/overview/assets/log-forwarding/common/onboard-button.png)

## 手順 2：標準ログを有効にする（CloudFront コンソール） {#step-2}

標準ログを有効にするには、[AWS 管理コンソール](https://aws.amazon.com/console/)から次の手順に従います。

1. [CloudFront コンソール](https://console.aws.amazon.com/cloudfront/v4/home)にアクセスし、[既存の配分を更新](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/HowToUpdateDistribution.html#HowToUpdateDistributionProcedure)します。

1. 「**ログ**」タブを開きます。

1. 「**追加**」を選択し、ログを受信するサービス（この場合は **Amazon S3**）を選択します。

1. **宛先**&#x200B;で、リソースを選択または作成します。 **バケット名**&#x200B;を入力します。値は LLM Optimizer CDN 設定ページからコピーできます。

   ![CloudFront バケット名](/help/overview/assets/log-forwarding/cloudfront/cloudfront-bucket-name.png)

1. **追加設定**&#x200B;を指定します。

   - **フィールド選択** - ログファイルフィールドを選択します。 LLM Optimizer CDN 設定ページの必須フィールドを参照してください。

     ![CloudFront フィールドの選択](/help/overview/assets/log-forwarding/cloudfront/cloudfront-field-selection.png)

   - **パーティション** - LLM Optimizer 設定ページから&#x200B;**パス接頭辞**&#x200B;をコピーします。

     ![CloudFront パーティション](/help/overview/assets/log-forwarding/cloudfront/cloudfront-partitioning.png)

   - **出力形式** - 形式は JSON にする必要があります。

     ![CloudFront 出力形式](/help/overview/assets/log-forwarding/cloudfront/cloudfront-output-format.png)

1. 配分を更新または作成する手順を完了します。

1. **ログ** ページで、**有効**&#x200B;が配分の横に表示されていることを確認します。

## クロスアカウント配信の標準ログを有効にする {#cross-account}

**ソースアカウント**（CloudFront 配分を使用）は、アクセスログを&#x200B;**宛先アカウント**（LLM Optimizer CDN 設定ページに表示されている S3 バケット）に送信します。 両方のアカウントに適切な権限が必要です。

例：ソースアカウント `111111111111` は、宛先アカウント `222222222222` の S3 バケットにログを送信します。 [AWS コマンドラインインターフェイス](https://aws.amazon.com/cli/)を使用できます。

>[!NOTE]
>
>以下のコマンドでは、配信の宛先 ARN の値（`arn:aws:logs:us-east-1:222222222222:delivery-destination:cloudfront-delivery-destination`）を、LLM Optimizer の設定ページの&#x200B;**配信の宛先 ARN** の値に置き換えます。

![配信の宛先 ARN](/help/overview/assets/log-forwarding/cloudfront/cloudfront-delivery-destination-arn.png)

### ソースアカウントの設定 {#source-account}

次に、ソースアカウントを設定する必要があります。

1. **配信ソースを作成** - 名前と配分 ARNを置き換えます。

   ```bash
   aws logs put-delivery-source --name s3-cf-delivery \
     --resource-arn arn:aws:cloudfront::111111111111:distribution/E1TR1RHV123ABC \
     --log-type ACCESS_LOGS
   ```

1. **配信を作成** - ソースを宛先にリンクします。「宛先アカウントの設定」手順の宛先 ARN を使用します。

   ```bash
   aws logs create-delivery --delivery-source-name s3-cf-delivery \
     --delivery-destination-arn arn:aws:logs:us-east-1:222222222222:delivery-destination:cloudfront-delivery-destination
   ```

1. **検証：**

   - **ソース**&#x200B;アカウントの場合：CloudFront コンソール／配分／**ログ**&#x200B;タブ。 **タイプ**&#x200B;の下に、S3 クロスアカウントログ配信が表示されます。
   - **宛先**&#x200B;アカウントの場合：S3 コンソール／バケット。 そのフォルダー内に、接頭辞（例：`MyLogPrefix`）とログが表示されます。
