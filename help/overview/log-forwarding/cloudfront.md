---
title: ログ転送 – CloudFront
description: CloudFrontからAdobeにCDN ログを転送する方法について説明します。エージェント型トラフィックデータ収集のためにLLM OptimizerでS3 バケットを使用します。
feature: Agentic Traffic
autotag-review: '2026-05-15T17:43:07.178Z'
TQID: 'https://experienceleague.adobe.com/TXnY-eK1SUuKrlVoGWd2hZO5bjUqEspvyFmcyOuei3Q'
product_v2:
  - id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2:
  - id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2:
  - id: d23587d6-14d6-4e3f-9ee1-cc18623832e1
  - id: e69d5a42-0217-4ca5-9396-a9a826a170da
topic_v2:
  - id: d3cdead0-685a-4489-9250-4bb709942f66
  - id: e1e0219c-f879-479f-8427-888ed2a6e9c2
source-git-commit: 7a92587197cf6a9eec6b01bd4eaeeaf1194d3088
workflow-type: tm+mt
source-wordcount: 466
ht-degree: 0%

---


# ログ転送：CloudFront {#log-forwarding-cloudfront}

このページでは、エージェント型トラフィックデータ収集用に、CloudFrontからAdobeのS3 バケットにCDN ログを転送する方法について説明します。 LLM Optimizer CDN設定ページを使用して、LLM Optimizerにオンボーディングします。 オンボーディングプロセスが完了したら、このページに記載されている手順に従って、CloudFront ダッシュボードコンソールでログ転送を設定します。

## ステップ 1: LLM Optimizerでのオンボーディング {#step-1}

LLM Optimizer ページ [https://llmo.now/](https://llmo.now/)で、次の操作を行います。

1. **顧客設定ダッシュボード**&#x200B;に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN設定**」タブをクリックします。

   ![CDN設定タブ &#x200B;](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)

1. 「**基本を学ぶ**」をクリックします。

   <!-- ![Onboard CDN button](/help/overview/assets/log-forwarding/common/onboard-cdn-button.png)-->

1. 「**AI トラフィックインサイトをアクティブ化**」の横にある「**設定**」をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)

1. **AWS アカウント** IDを入力してください。

   ![AWS アカウント ID](/help/overview/assets/log-forwarding/cloudfront/cloudfront-aws-account.png)

1. **CloudFront （BYOCDN）**&#x200B;を選択します。

   ![CloudFrontを選択](/help/overview/assets/log-forwarding/cloudfront/cloudfront-select.png)

1. 「**オンボード**」をクリックします。

   ![&#x200B; オンボードボタン &#x200B;](/help/overview/assets/log-forwarding/common/onboard-button.png)

## 手順2：標準ログ記録を有効にする（CloudFront コンソール） {#step-2}

標準ログを有効にするには、[AWS管理コンソール &#x200B;](https://aws.amazon.com/console/)から次の操作を行います。

1. [CloudFront コンソール &#x200B;](https://console.aws.amazon.com/cloudfront/v4/home)にアクセスし、[既存の配布](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/HowToUpdateDistribution.html#HowToUpdateDistributionProcedure)を更新します。

1. 「**ログ**」タブを開きます。

1. **Add**&#x200B;を選択し、ログを受信するサービス（この場合は&#x200B;**Amazon S3**）を選択します。

1. **宛先**&#x200B;で、リソースを選択または作成します。 **バケット名**&#x200B;を入力します。LLM Optimizer CDN設定ページから値をコピーできます。

   ![CloudFront バケット名](/help/overview/assets/log-forwarding/cloudfront/cloudfront-bucket-name.png)

1. **追加設定**&#x200B;を構成します。

   - **フィールド選択** — ログファイルフィールドを選択します。 LLM Optimizer CDN設定ページの必須フィールドを参照してください。

     ![CloudFront フィールドの選択](/help/overview/assets/log-forwarding/cloudfront/cloudfront-field-selection.png)

   - **パーティション設定** — LLM Optimizer設定ページから&#x200B;**パス サフィックス**&#x200B;をコピーします。

     ![CloudFront パーティション設定](/help/overview/assets/log-forwarding/cloudfront/cloudfront-partitioning.png)

   - **出力形式** – 形式はJSONである必要があります。

     ![CloudFront出力形式](/help/overview/assets/log-forwarding/cloudfront/cloudfront-output-format.png)

1. 配布を更新または作成する手順を完了します。

1. **ログ** ページで、**有効**&#x200B;がディストリビューションの横に表示されていることを確認します。

## クロスアカウント配信の標準ログ記録を有効にする {#cross-account}

**ソースアカウント** （CloudFront ディストリビューションを使用）は、**宛先アカウント** （LLM Optimizer CDN設定ページに表示されているS3 バケット）にアクセスログを送信します。 両方のアカウントに適切な権限が必要です。

例：ソースアカウント `111111111111`は、宛先アカウント `222222222222`のS3 バケットにログを送信します。 [AWS コマンドライン インターフェイス &#x200B;](https://aws.amazon.com/cli/)を使用できます。

>[!NOTE]
>
>以下のコマンドで、配信先のARN値（`arn:aws:logs:us-east-1:222222222222:delivery-destination:cloudfront-delivery-destination`）をLLM Optimizer設定ページの&#x200B;**配信先のARN**&#x200B;の値に置き換えます。

![配信先ARN](/help/overview/assets/log-forwarding/cloudfront/cloudfront-delivery-destination-arn.png)

### ソースアカウントの設定 {#source-account}

次に、ソースアカウントを設定する必要があります。

1. **配信ソースを作成** – 名前と配信ARNを置き換えます：

   ```bash
   aws logs put-delivery-source --name s3-cf-delivery \
     --resource-arn arn:aws:cloudfront::111111111111:distribution/E1TR1RHV123ABC \
     --log-type ACCESS_LOGS
   ```

1. **配信を作成** - ソースを宛先にリンクします。「宛先アカウントの設定」ステップで宛先ARNを使用します。

   ```bash
   aws logs create-delivery --delivery-source-name s3-cf-delivery \
     --delivery-destination-arn arn:aws:logs:us-east-1:222222222222:delivery-destination:cloudfront-delivery-destination
   ```

1. **検証：**

   - **source** アカウントで、CloudFront コンソール/ディストリビューション/「**ログ**」タブを選択します。 **Type**&#x200B;の下に、S3 クロスアカウントログの配信が表示されます。
   - **destination** アカウントで、S3 コンソール / バケットを選択します。 接頭辞（`MyLogPrefix`など）とそのフォルダー内のログが表示されます。
