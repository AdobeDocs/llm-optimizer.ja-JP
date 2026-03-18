---
title: ログ転送 – Cloudflare
description: LLM Optimizerでエージェンティックトラフィックデータを収集するために、CDN ログを Cloudflare からAdobeの S3 バケットに転送する方法を説明します。
feature: Agentic Traffic
source-git-commit: b590cd14ba7d64e56a6c972fd6090e2df9de58f6
workflow-type: tm+mt
source-wordcount: '381'
ht-degree: 0%

---


# ログ転送：Cloudflare {#log-forwarding-cloudflare}

このページでは、Cloudflare からAdobeの S3 バケットに CDN ログを転送してアジェンティックトラフィックデータを収集する方法について詳しく説明します。 LLM Optimizer CDN 設定ページを使用して、LLM Optimizerにオンボードします。 オンボーディングプロセスが完了したら、このページに記載されている手順に従って、Cloudflare ダッシュボードコンソールでログ転送を設定します。

## 手順 1:LLM Optimizerへのオンボード {#step-1}

LLM Optimizerページ [https://llmo.now/](https://llmo.now/) で、次の操作を行います。

1. **顧客設定ダッシュボード** に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN 設定**」タブをクリックします。

   ![CDN 設定タブ &#x200B;](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)

1. **開始** をクリックします。

   <!-- ![Onboard CDN button](/help/overview/assets/log-forwarding/common/onboard-cdn-button.png) -->

1. **AI トラフィックインサイトをアクティブ化** の横にある **設定** をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)

1. **Cloudflare （BYOCDN）** を選択します。

   ![Cloudflare を選択 &#x200B;](/help/overview/assets/log-forwarding/cloudflare/cloudflare-select.png)

1. **オンボード** をクリックします。

   <!-- ![Onboard button](/help/overview/assets/log-forwarding/common/onboard-button.png)-->

## 手順 2:Cloudflare で Logpush ジョブを作成する {#step-2}

[Cloudflare ダッシュボード &#x200B;](https://dash.cloudflare.com/login) で、次の手順に従います。

1. **ドメイン（ゾーン）** レベルの **Logpush** ページに移動します。
1. **ログプッシュジョブを作成** を選択します。
1. **宛先を選択** で、「**Amazon S3**」を選択します。
1. 次の宛先情報を入力します。

   - **バケット** - S3 バケット名。 LLM Optimizer CDN 設定ページから値をコピーします。

     ![&#x200B; バケット名 &#x200B;](/help/overview/assets/log-forwarding/common/bucket-name.png)

   - **パス** - ストレージコンテナ内のバケットの場所。 LLM Optimizer CDN 設定ページから値をコピーします。

     ![Cloudflare パス &#x200B;](/help/overview/assets/log-forwarding/cloudflare/cloudflare-path.png)

   - **ログを毎日のサブフォルダーに整理** （推奨）。

     ![&#x200B; 日別サブフォルダー &#x200B;](/help/overview/assets/log-forwarding/cloudflare/cloudflare-daily-subfolders.png)

   - **バケットリージョン** — LLM Optimizer CDN 設定ページから値をコピーします。

     <!-- ![Region](/help/overview/assets/log-forwarding/cloudflare/cloudflare-region.png)-->

   - サーバー側の暗号化が必要ない場合は、オフのままにします。

   上記の手順を完了したら、「**続行**」を選択します。

1. 所有権を証明するために、Cloudflare は指定された宛先にファイルを送信します。 トークンを見つけるには、所有権チャレンジファイルの **概要** タブにある **開く** ボタンをクリックします。 LLM Optimizer CDN 設定ページから所有権トークンをコピーし、Cloudflare ダッシュボードに貼り付けて、バケットへのアクセスを確認します。 所有権トークンを入力し、「**続行**」を選択します。

   <!--![Ownership token](/help/overview/assets/log-forwarding/cloudflare/cloudflare-ownership-token.png)-->

1. ストレージサービスにプッシュする **HTTP リクエスト** データセットを選択します。

1. ログプッシュジョブを設定します。

   - **ジョブ名** を入力します。

   - **次のフィールドを送信** で、LLM Optimizer設定ページの値を参照してください。

     ![Logpush フィールド &#x200B;](/help/overview/assets/log-forwarding/cloudflare/cloudflare-logpush-fields.png)

   - **ログ形式**:JSON

     <!--![JSON format](/help/overview/assets/log-forwarding/cloudflare/cloudflare-json-format.png)-->

1. **詳細オプション** で、次の操作を行います。

   - ログのタイムスタンプフィールドの形式を次のように選択します。`RFC3339`

     ![&#x200B; タイムスタンプ形式 &#x200B;](/help/overview/assets/log-forwarding/cloudflare/cloudflare-timestamp-format.png)

   - サンプリングレートには、「**すべてのログ**」を選択します。

     ![&#x200B; サンプリングレート &#x200B;](/help/overview/assets/log-forwarding/cloudflare/cloudflare-sampling-rate.png)

1. Logpush ジョブの設定が完了したら、**送信** を選択します。
