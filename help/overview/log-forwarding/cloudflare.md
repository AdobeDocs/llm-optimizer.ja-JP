---
title: ログ転送 – Cloudflare
description: CloudflareからAdobeのS3 バケットにCDN ログを転送して、LLM Optimizerでエージェンティックトラフィックデータを収集する方法を説明します。
feature: Agentic Traffic
autotag-review: '2026-05-15T17:41:23.688Z'
TQID: 'https://experienceleague.adobe.com/AfhcMa7tZ3L-4qCbNKiblInALmHaKxWLtL-O-Hkvc-U'
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2: id: d23587d6-14d6-4e3f-9ee1-cc18623832e1
topic_v2: id: d3cdead0-685a-4489-9250-4bb709942f66id: e1e0219c-f879-479f-8427-888ed2a6e9c2
source-git-commit: 7a92587197cf6a9eec6b01bd4eaeeaf1194d3088
workflow-type: tm+mt
source-wordcount: 381
ht-degree: 0%

---


# ログ転送：Cloudflare {#log-forwarding-cloudflare}

このページでは、エージェント型トラフィックデータ収集用にCloudflareからAdobe S3 バケットにCDN ログを転送する方法について詳しく説明します。 LLM Optimizer CDN設定ページを使用して、LLM Optimizerにオンボーディングします。 オンボーディングプロセスが完了したら、このページに記載されている手順に従って、Cloudflare ダッシュボードコンソールでログ転送を設定します。

## ステップ 1: LLM Optimizerでのオンボーディング {#step-1}

LLM Optimizer ページ [https://llmo.now/](https://llmo.now/)で、次の操作を行います。

1. **顧客設定ダッシュボード**&#x200B;に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN設定**」タブをクリックします。

   ![CDN設定タブ ](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)

1. 「**基本を学ぶ**」をクリックします。

   <!-- ![Onboard CDN button](/help/overview/assets/log-forwarding/common/onboard-cdn-button.png) -->

1. 「**AI トラフィックインサイトをアクティブ化**」の横にある「**設定**」をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)

1. **Cloudflare （BYOCDN）**&#x200B;を選択します。

   ![Cloudflareを選択](/help/overview/assets/log-forwarding/cloudflare/cloudflare-select.png)

1. 「**オンボード**」をクリックします。

   <!-- ![Onboard button](/help/overview/assets/log-forwarding/common/onboard-button.png)-->

## ステップ 2:CloudflareでLogpush ジョブを作成する {#step-2}

[Cloudflare ダッシュボード ](https://dash.cloudflare.com/login)で、次の手順に従います。

1. **ドメイン （ゾーン）** レベルの&#x200B;**Logpush** ページに移動します。
1. **ログプッシュジョブの作成**&#x200B;を選択します。
1. **宛先を選択**&#x200B;で、**Amazon S3**&#x200B;を選択します。
1. 次の宛先情報を入力します。

   - **Bucket** — S3 バケット名。 LLM Optimizer CDN Configuration ページから値をコピーします。

     ![ バケット名](/help/overview/assets/log-forwarding/common/bucket-name.png)

   - **パス** — ストレージコンテナ内のバケットの場所。 LLM Optimizer CDN Configuration ページから値をコピーします。

     ![Cloudflare パス ](/help/overview/assets/log-forwarding/cloudflare/cloudflare-path.png)

   - **ログを毎日のサブフォルダー**&#x200B;に整理します（推奨）。

     ![日別サブフォルダー](/help/overview/assets/log-forwarding/cloudflare/cloudflare-daily-subfolders.png)

   - **バケット領域** — LLM Optimizer CDN Configuration ページから値をコピーします。

     <!-- ![Region](/help/overview/assets/log-forwarding/cloudflare/cloudflare-region.png)-->

   - サーバーサイドの暗号化が必要ない場合は、オフのままにします。

   上記の手順を完了したら、**続行**&#x200B;を選択します。

1. 所有権を証明するために、Cloudflareは指定した宛先にファイルを送信します。 トークンを見つけるには、所有権チャレンジファイルの「**概要**」タブにある「**開く**」ボタンをクリックします。 LLM Optimizer CDN設定ページから所有権トークンをコピーし、それをCloudflare ダッシュボードに貼り付けて、バケットへのアクセスを確認します。 所有権トークンを入力し、**続行**&#x200B;を選択します。

   <!--![Ownership token](/help/overview/assets/log-forwarding/cloudflare/cloudflare-ownership-token.png)-->

1. ストレージ サービスにプッシュする&#x200B;**HTTP Requests** データセットを選択します。

1. Logpush ジョブを設定します。

   - **ジョブ名**&#x200B;を入力します。

   - **次のフィールドを送信する**&#x200B;で、LLM Optimizer設定ページの値を参照してください。

     ![ ログプッシュフィールド ](/help/overview/assets/log-forwarding/cloudflare/cloudflare-logpush-fields.png)

   - **ログ形式**: JSON。

     <!--![JSON format](/help/overview/assets/log-forwarding/cloudflare/cloudflare-json-format.png)-->

1. **詳細オプション**&#x200B;で：

   - ログのタイムスタンプフィールドの形式を選択してください：`RFC3339`。

     ![ タイムスタンプ形式](/help/overview/assets/log-forwarding/cloudflare/cloudflare-timestamp-format.png)

   - サンプリングレートの場合は、**すべてのログ**&#x200B;を選択します。

     ![ サンプリングレート ](/help/overview/assets/log-forwarding/cloudflare/cloudflare-sampling-rate.png)

1. ログプッシュジョブの設定が完了したら、**送信**&#x200B;を選択します。
