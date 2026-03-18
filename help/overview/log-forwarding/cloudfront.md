---
title: ログ転送 – CloudFront
description: LLM Optimizerでアジェンティックトラフィックデータを収集するために CloudFront からAdobeの S3 バケットに CDN ログを転送する方法を説明します。
feature: Agentic Traffic
source-git-commit: d1f98770b39f550c36d93ece9b89933c0e90f189
workflow-type: tm+mt
source-wordcount: '466'
ht-degree: 0%

---


# ログ転送：CloudFront {#log-forwarding-cloudfront}

このページでは、CloudFront からAdobeの S3 バケットに CDN ログを転送してアジェンティックトラフィックデータを収集する方法について説明します。 LLM Optimizer CDN 設定ページを使用して、LLM Optimizerにオンボードします。 オンボーディングプロセスが完了したら、このページで説明する手順に従って、CloudFront ダッシュボードコンソールでログ転送を設定します。

## 手順 1:LLM Optimizerへのオンボード {#step-1}

LLM Optimizerページ [https://llmo.now/](https://llmo.now/) で、次の操作を行います。

1. **顧客設定ダッシュボード** に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN 設定**」タブをクリックします。

   ![CDN 設定タブ &#x200B;](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)

1. **開始** をクリックします。

   <!-- ![Onboard CDN button](/help/overview/assets/log-forwarding/common/onboard-cdn-button.png)-->

1. **AI トラフィックインサイトをアクティブ化** の横にある **設定** をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)

1. **AWS アカウント** ID を入力します。

   ![AWS アカウント ID](/help/overview/assets/log-forwarding/cloudfront/cloudfront-aws-account.png)

1. 「**CloudFront （BYOCDN）**」を選択します。

   ![CloudFront を選択 &#x200B;](/help/overview/assets/log-forwarding/cloudfront/cloudfront-select.png)

1. **オンボード** をクリックします。

   ![&#x200B; オンボードボタン &#x200B;](/help/overview/assets/log-forwarding/common/onboard-button.png)

## 手順 2：標準ログを有効にする（CloudFront コンソール） {#step-2}

標準ログを有効にするには、[AWS Management Console](https://aws.amazon.com/console/) から次の操作を行います。

1. [CloudFront コンソール &#x200B;](https://console.aws.amazon.com/cloudfront/v4/home) にアクセスし、[&#x200B; 既存の配布を更新 &#x200B;](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/HowToUpdateDistribution.html#HowToUpdateDistributionProcedure) します。

1. 「**ログ** タブを開きます。

1. 「**追加**」を選択してから、ログを受け取るサービス（この場合は **Amazon S3**）を選択します。

1. **宛先** については、リソースを選択または作成します。 **バケット名** を入力すると、LLM Optimizer CDN 設定ページから値をコピーできます。

   ![CloudFront バケット名 &#x200B;](/help/overview/assets/log-forwarding/cloudfront/cloudfront-bucket-name.png)

1. 設定 **追加設定**:

   - **フィールドの選択** — ログ ファイルのフィールドを選択します。 LLM Optimizer CDN 設定ページの必須フィールドを参照してください。

     ![CloudFront フィールドの選択 &#x200B;](/help/overview/assets/log-forwarding/cloudfront/cloudfront-field-selection.png)

   - **パーティション化** — LLM Optimizer設定ページから **path サフィックス** をコピーします。

     ![CloudFront のパーティション設定 &#x200B;](/help/overview/assets/log-forwarding/cloudfront/cloudfront-partitioning.png)

   - **出力形式** – 形式は JSON にする必要があります。

     ![CloudFront 出力形式 &#x200B;](/help/overview/assets/log-forwarding/cloudfront/cloudfront-output-format.png)

1. 配布を更新または作成するには、次の手順を実行します。

1. **ログ** ページで、配布の横に **有効** が表示されていることを確認します。

## クロスアカウント配信の標準ログを有効にする {#cross-account}

（CloudFront 配布の **ソースアカウント** は、アクセスログを **宛先アカウント** （LLM Optimizer CDN 設定ページに表示される S3 バケット）に送信します。 両方のアカウントに適切な権限が必要です。

例：ソースアカウント `111111111111` は、宛先アカウント `222222222222` の S3 バケットにログを送信します。 [AWS Commad Line インターフェイス &#x200B;](https://aws.amazon.com/cli/) を使用できます。

>[!NOTE]
>
>以下のコマンドでは、配信先の ARN 値（`arn:aws:logs:us-east-1:222222222222:delivery-destination:cloudfront-delivery-destination`）を、LLM Optimizerの設定ページにある **配信先 ARN** の値に置き換えます。

![&#x200B; 配信先 ARN](/help/overview/assets/log-forwarding/cloudfront/cloudfront-delivery-destination-arn.png)

### ソースアカウントの設定 {#source-account}

次に、ソースアカウントを設定する必要があります。

1. **配信ソースの作成** – 名前と配信 ARN を

   ```bash
   aws logs put-delivery-source --name s3-cf-delivery \
     --resource-arn arn:aws:cloudfront::111111111111:distribution/E1TR1RHV123ABC \
     --log-type ACCESS_LOGS
   ```

1. **配信の作成** - ソースを宛先にリンクします。「宛先アカウントの設定」手順の宛先 ARN を使用します。

   ```bash
   aws logs create-delivery --delivery-source-name s3-cf-delivery \
     --delivery-destination-arn arn:aws:logs:us-east-1:222222222222:delivery-destination:cloudfront-delivery-destination
   ```

1. **検証：**

   - **ソース** アカウントで：CloudFront コンソール/配布版/「**ログ**」タブ **タイプ** の下に、S3 クロスアカウントログ配信が表示されます。
   - **宛先** アカウントで、S3 コンソール/バケットを選択します。 そのフォルダーにプレフィックス（例：`MyLogPrefix`）とログが表示されます。
