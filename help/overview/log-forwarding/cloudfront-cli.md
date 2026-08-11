---
title: ログ転送 - CloudFront（AWS CLI）
description: 配信の設定と操作に AWS CLI を使用して、CloudFront CDN ログをアドビの S3 バケットに転送します。
feature: Agentic Traffic
autotag-review: '2026-07-15T17:46:48.888Z'
TQID: 'https://experienceleague.adobe.com/g3et1xycA6wCBl6hZ8CPVG9i0IppDN-SashlekY6pNM'
product_v2:
  - id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2:
  - id: d1956731-2adb-4bb7-8301-2b239254ac72
  - id: e0828736-236a-487b-a478-5a635455eadc
subfeature_v2:
  - id: d23587d6-14d6-4e3f-9ee1-cc18623832e1
  - id: e69d5a42-0217-4ca5-9396-a9a826a170da
  - id: e06fae5f-830b-4222-a469-b5e148d36465
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: d095671a-1355-40aa-8b5f-06c33c68080b
  - id: d3cdead0-685a-4489-9250-4bb709942f66
  - id: e1e0219c-f879-479f-8427-888ed2a6e9c2
  - id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 2705cf26faea9c09817bbdcec4b4c531552df7ba
workflow-type: ht
source-wordcount: 379
ht-degree: 100%

---


# ログ転送：CloudFront（AWS CLI） {#log-forwarding-cloudfront-cli}

このページでは、エージェントトラフィックのデータ収集において、CloudFront から CDN ログをアドビの S3 バケットに転送する方法について説明します。 LLM Optimizer にオンボードするには、LLM Optimizer CDN 設定ページを使用します。 オンボーディングプロセスが完了したら、このページに記載されている手順に従って、[手順 2](#step-2-cli) の [AWS コマンドラインインターフェイス](https://aws.amazon.com/cli/)を使用してログ転送を設定します。

>[!NOTE]
>
> このガイドでは、[AWS コマンドラインインターフェイス](https://aws.amazon.com/cli/)を使用してログ転送を設定する方法について説明します。 **CloudFront UI** を使用してログ転送を設定する場合は、[ログ転送：CloudFront](/help/overview/log-forwarding/cloudfront.md) を参照してください。

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

<!--  ![AWS Account ID](/help/overview/assets/log-forwarding/cloudfront/cloudfront-aws-account.png)-->

1. 「**CloudFront（BYOCDN）**」を選択します。

   ![CloudFront を選択](/help/overview/assets/log-forwarding/cloudfront/cloudfront-select.png)

1. 「**オンボード**」をクリックします。

<!-- ![Onboard button](/help/overview/assets/log-forwarding/common/onboard-button.png)-->

## 手順 2：AWS CLI を使用して CDN ログ転送を設定 {#step-2-cli}

AWS CLI を使用して CDN ログ転送を設定するには、次の手順に従います。

### AWS CLI 資格情報の設定

AWS CLI 資格情報 MAC を設定します。 ～/.aws/credentials を開き、以下の変数の値を入力します。

```text
[LLMO]
aws_access_key_id=<VALUE_OF_ACCESS_KEY_ID>
aws_secret_access_key=<VALUE_OF_SECRET_KEY>
aws_session_token=<ONLY_IF_USING_SECURITY_TOKEN_SERVICE> ## Optional
```

### 接続のテスト

接続をテストするには、以下のコマンドを実行します。

```bash
aws sts get-caller-identity --profile LLMO
```

正常な出力の例：

```bash
aws sts get-caller-identity --profile LLMO
{
    "UserId": "AxxxxxxxxxxxP:user",
    "Account": "012345678912",
    "Arn": "arn:aws:sts::012345678912:assumed-role/klam-master-role-BatlY3dnPVinQLC/user"
}
```

### 変数の初期化

`REPLACEME123@AdobeOrg` を組織の Adobe IMS 組織 ID に置き換えて、以下のコマンドを実行します。 このコマンドの出力 ID は、`TRANSFORM_IMS_ID` と呼ばれます。

```bash
echo "REPLACEME123@AdobeOrg" | sed 's/@AdobeOrg$//' | tr '[:upper:]' '[:lower:]'
```

以下のガイドラインに従って、`CUSTOMER`、`CDN_ID`、`ACCT1`、`TRANSFORM_IMS_ID` の値を入力し、ターミナルからコマンドを実行します。

```bash
export PROFILE1=LLMO
export REGION1=us-east-1
export CUSTOMER=<CUSTOMER_NAME> ## No Space, user letters,numbers and dash
export CDN_ID=<YOUR_CLOUDFRONT_DISTRIBUTION_ID>
export ACCT1=<YOUR_AWS_ACCOUNT_NUMBER>
export DELIVERY_DEST_ARN=arn:aws:logs:us-east-1:640168421876:delivery-destination:cdn-logs-<TRANSFORM_IMS_ID>  ## Replace TRANSFORM_IMS_ID with the output of the command above
```

<!--Use the **Delivery destination ARN** and org values from the LLM Optimizer CDN configuration page if they differ from the pattern above.-->

### 配信ソースの作成

手順 3 を実行したのと同じターミナルから、以下のコマンドを実行します。

```bash
aws logs put-delivery-source --name llmo-cf-${CUSTOMER}-${CDN_ID} \
  --profile $PROFILE1 --region $REGION1 \
  --resource-arn arn:aws:cloudfront::${ACCT1}:distribution/${CDN_ID} \
  --log-type ACCESS_LOGS
```

>[!IMPORTANT]
>
>次のエラーが発生した場合は、既存の配信ソースを検索します。*PutDeliverySource 操作の呼び出しの際にエラーが発生しました（ConflictException）：この ResourceId は、このアカウントの別の配信ソースで既に使用されています。*
>
>既存の配送ソースを検索するには、次のコマンドを実行します。
>
>```bash
>aws logs describe-delivery-sources --region us-east-1 \
>--query "deliverySources[?contains(resourceArns[0], '<CDN DistributionID>')]"
>```
>
>次のコマンドでは、上記のコマンドの結果から配信ソース名を使用します。

### 配信設定の作成

```bash
aws logs create-delivery \
  --profile "$PROFILE1" --region "$REGION1" \
  --delivery-source-name "llmo-cf-${CUSTOMER}-${CDN_ID}" \
  --delivery-destination-arn $DELIVERY_DEST_ARN \
  --s3-delivery-configuration '{"suffixPath":"/{yyyy}/{MM}/{dd}/{HH}"}' \
  --record-fields 'date' 'time' 'x-edge-location' 'cs-method' 'cs(Host)' 'cs-uri-stem' 'sc-status' 'cs(Referer)' 'cs(User-Agent)' 'time-to-first-byte' 'sc-content-type' 'x-host-header'
```

&lt;!--ドキュメントや製品値が変更された場合は、`--record-fields` と `--s3-delivery-configuration` を LLM Optimizer CDN 設定ページに表示されているフィールドリストとパス接頭辞に一致させます。-->
