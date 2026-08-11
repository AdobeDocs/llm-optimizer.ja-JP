---
title: ログ転送 - Cloudflare
description: LLM Optimizer でのエージェントトラフィックのデータ収集において、Cloudflare からアドビの S3 バケットに CDN ログを転送する方法について説明します。
feature: Agentic Traffic
autotag-review: '2026-07-15T17:45:52.167Z'
TQID: 'https://experienceleague.adobe.com/6D7Xe-ysQOSsNMONyart2HaTfdyQR64l0r3AeFNsOpI'
product_v2:
  - id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2:
  - id: d1956731-2adb-4bb7-8301-2b239254ac72
  - id: e0828736-236a-487b-a478-5a635455eadc
subfeature_v2:
  - id: d23587d6-14d6-4e3f-9ee1-cc18623832e1
  - id: e06fae5f-830b-4222-a469-b5e148d36465
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: d3cdead0-685a-4489-9250-4bb709942f66
  - id: e1e0219c-f879-479f-8427-888ed2a6e9c2
  - id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 2705cf26faea9c09817bbdcec4b4c531552df7ba
workflow-type: ht
source-wordcount: 381
ht-degree: 100%

---


# ログ転送：Cloudflare {#log-forwarding-cloudflare}

このページでは、エージェントトラフィックのデータ収集において、Cloudflare からアドビの S3 バケットに CDN ログを転送する方法について説明します。 LLM Optimizer にオンボードするには、LLM Optimizer CDN 設定ページを使用します。 オンボーディングプロセスが完了したら、このページに記載されている手順に従って、Cloudflare ダッシュボードでログ転送を設定します。

## 手順 1：LLM Optimizer でオンボード {#step-1}

LLM Optimizer ページ [https://llmo.now/](https://llmo.now/) で、次の手順に従います。

1. **顧客設定ダッシュボード**&#x200B;に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN 設定**」タブをクリックします。

   ![「CDN 設定」タブ](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)

1. 「**開始**」をクリックします。

   <!-- ![Onboard CDN button](/help/overview/assets/log-forwarding/common/onboard-cdn-button.png) -->

1. 「**AI トラフィックインサイトをアクティブ化**」の横にある「**設定**」をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)

1. 「**Cloudflare（BYOCDN）**」を選択します。

   ![「Cloudflare」を選択](/help/overview/assets/log-forwarding/cloudflare/cloudflare-select.png)

1. 「**オンボード**」をクリックします。

   <!-- ![Onboard button](/help/overview/assets/log-forwarding/common/onboard-button.png)-->

## 手順 2：Cloudflare で Logpush ジョブを作成 {#step-2}

[Cloudflare ダッシュボード](https://dash.cloudflare.com/login)で、次の手順に従います。

1. **ドメイン（ゾーン）**&#x200B;レベルの **Logpush** ページに移動します。
1. 「**Logpush ジョブを作成**」を選択します。
1. **宛先を選択**&#x200B;で、「**Amazon S3**」を選択します。
1. 次の宛先情報を入力します。

   - **バケット** - S3 バケット名。 LLM Optimizer CDN 設定ページから値をコピーします。

     ![バケット名](/help/overview/assets/log-forwarding/common/bucket-name.png)

   - **パス** - ストレージコンテナ内のバケットの場所。 LLM Optimizer CDN 設定ページから値をコピーします。

     ![Cloudflare パス](/help/overview/assets/log-forwarding/cloudflare/cloudflare-path.png)

   - **ログを毎日のサブフォルダーに整理します**（推奨）。

     ![毎日のサブフォルダー](/help/overview/assets/log-forwarding/cloudflare/cloudflare-daily-subfolders.png)

   - **バケット領域** - LLM Optimizer CDN 設定ページから値をコピーします。

     <!-- ![Region](/help/overview/assets/log-forwarding/cloudflare/cloudflare-region.png)-->

   - サーバーサイドの暗号化が必要ない場合は、オフのままにします。

   上記の手順を完了したら、「**続行**」を選択します。

1. 所有権を証明するために、Cloudflare は指定された宛先にファイルを送信します。 トークンを見つけるには、所有権課題ファイルの「**概要**」タブにある「**開く**」ボタンをクリックします。 LLM Optimizer CDN 設定ページから所有権トークンをコピーし、Cloudflare ダッシュボードにペーストして、バケットへのアクセス権を確認します。 所有権トークンを入力し、「**続行**」を選択します。

   <!--![Ownership token](/help/overview/assets/log-forwarding/cloudflare/cloudflare-ownership-token.png)-->

1. ストレージサービスにプッシュする **HTTP リクエスト**&#x200B;データセットを選択します。

1. Logpush ジョブを設定します。

   - **ジョブ名**&#x200B;を入力します。

   - **次のフィールドを送信**&#x200B;で、LLM Optimizer 設定ページにある値を参照します。

     ![Logpush フィールド](/help/overview/assets/log-forwarding/cloudflare/cloudflare-logpush-fields.png)

   - **ログ形式**：JSON。

     <!--![JSON format](/help/overview/assets/log-forwarding/cloudflare/cloudflare-json-format.png)-->

1. **詳細オプション**&#x200B;で、次の操作を実行します。

   - ログのタイムスタンプフィールドの形式（`RFC3339`）を選択します。

     ![タイムスタンプ形式](/help/overview/assets/log-forwarding/cloudflare/cloudflare-timestamp-format.png)

   - サンプリングレートの場合は、「**すべてのログ**」を選択します。

     ![サンプリングレート](/help/overview/assets/log-forwarding/cloudflare/cloudflare-sampling-rate.png)

1. Logpush ジョブの設定が完了したら、「**送信**」を選択します。
