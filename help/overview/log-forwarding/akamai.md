---
title: ログ転送 – Akamai
description: AkamaiからAdobeのS3 バケットにCDN ログを転送して、LLM Optimizerでエージェンティックトラフィックデータを収集する方法を説明します。
feature: Agentic Traffic
autotag-review: '2026-05-15T17:35:22.816Z'
TQID: 'https://experienceleague.adobe.com/cO-qqOveWFee1-QnVSlzmO-n383sptHl59Ni2qQcvAU'
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2: id: d23587d6-14d6-4e3f-9ee1-cc18623832e1
topic_v2: id: e1e0219c-f879-479f-8427-888ed2a6e9c2
source-git-commit: 7a92587197cf6a9eec6b01bd4eaeeaf1194d3088
workflow-type: tm+mt
source-wordcount: 595
ht-degree: 0%

---


# ログ転送：Akamai {#log-forwarding-akamai}

このページでは、エージェント型トラフィックデータ収集用にAkamaiからAdobeのS3 バケットにCDN ログを転送する方法について説明します。 LLM Optimizer CDN設定ページを使用して、LLM Optimizerにオンボーディングします。 オンボーディングプロセスが完了したら、このページに記載されている手順に従って、Akamai Campaign コントロールパネルでログ転送を設定します。

## ステップ 1: LLM Optimizerでのオンボーディング {#step-1}

LLM Optimizer ページ [https://llmo.now/](https://llmo.now/)で、次の操作を行います。

1. **顧客設定ダッシュボード**&#x200B;に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN設定**」タブをクリックします。

   ![CDN設定タブ ](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)

1. 「**基本を学ぶ**」をクリックします。

   <!--![Onboard CDN button](/help/overview/assets/log-forwarding/common/onboard-cdn-button.png)-->

1. 「**AI トラフィックインサイトをアクティブ化**」の横にある「**設定**」をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)

1. **Akamai （BYOCDN）**&#x200B;を選択します。

   ![Akamaiを選択](/help/overview/assets/log-forwarding/akamai/akamai-select.png)

1. 「**オンボード**」をクリックします。

   <!--![Onboard button](/help/overview/assets/log-forwarding/common/onboard-button.png)-->

## 手順2:Akamaiでストリームを作成する {#step-2}

Akamai コントロールパネル [https://control.akamai.com/](https://control.akamai.com/)で、Akamaiの公式ドキュメントの手順に従って[ ストリームを作成](https://techdocs.akamai.com/datastream2/docs/create-stream)します。

## 手順3：データパラメーターの選択 {#step-3}

ストリームを作成したら、Akamai コントロールパネルで「次へ」をクリックして、「**データセット**」タブに進みます。 Akamaiの公式ドキュメントの手順に従って、[ データパラメーター](https://techdocs.akamai.com/datastream2/docs/choose-data-parameters)を選択します。 LLM Optimizer設定の次のフィールドが必要になります。

![LLMO設定フィールド ](/help/overview/assets/log-forwarding/akamai/akamai-llmo-config-fields.png)

マッピングは次のようになります。

* **ログ情報**
reqTimeSec -> リクエスト時間
* **地域データ**
国/国/地域
* **メッセージ交換データ**
reqHost -> リクエストホスト
reqPath -> リクエストパス
queryStr -> クエリ文字列
reqMethod -> リクエストメソッド
ua -> User-Agent
statusCode -> HTTP ステータスコード
rspContentType -> Response Content-Type
* **リクエストヘッダーデータ**
referer -> Referer
* **ネットワーク パフォーマンス データ**
timeToFirstByte ->最初のバイトまでの時間

Akamaiのデータセットフィールド（IDを含む）は次のとおりです。

1100, # reqTimeSec -> リクエスト時間
2012, # country -> Country/Region
1011、# reqHost -> リクエストホスト
1013, # reqPath -> リクエストパス
2009, # queryStr -> クエリ文字列
1012, # reqMethod -> リクエストメソッド
1017, # ua -> User-Agent
1008、# statusCode -> HTTP ステータスコード
1032, # referer -> Referer
1016, # rspContentType -> Response Content-Type
2025 # timeToFirstByte ->最初のバイトまでの時間

## 手順4：宛先の設定 {#step-4}

データストリームを作成し、宛先を設定するために必要なパラメーターを選択した後。 宛先を設定するには、次の手順に従います。

1. **宛先**&#x200B;で、**S3**&#x200B;を選択します。
2. **名前**&#x200B;に、人間が判読可能な宛先の説明を入力します。
3. **Bucket**&#x200B;で、**Bucket Name**&#x200B;をLLM Optimizer設定ページからコピーします。

   ![ バケット名](/help/overview/assets/log-forwarding/common/bucket-name.png)

4. **フォルダーのパス**&#x200B;で、LLM Optimizerの設定ページから&#x200B;**パス**&#x200B;をコピーします。

   ![ パス設定](/help/overview/assets/log-forwarding/akamai/akamai-path-config.png)

5. **Region**&#x200B;で、**Region**&#x200B;をLLM Optimizer設定ページからコピーします。

   <!--![Region](/help/overview/assets/log-forwarding/common/region.png)-->

6. **アクセスキーID**&#x200B;と&#x200B;**秘密鍵**&#x200B;で、両方の値をLLM Optimizer設定ページからコピーします。

   ![ アクセスキー](/help/overview/assets/log-forwarding/common/access-keys.png)

7. 「**検証と保存**」をクリックして、宛先への接続を検証し、指定した詳細を保存します。 この検証プロセスの一環として、システムは提供されたアクセスキー識別子と秘密アクセスキーを使用して、ファイル名に`Akamai_access_verification_[TimeStamp].txt`形式のタイムスタンプを含むS3 フォルダーに検証ファイルを作成します。 このファイルは、検証プロセスが成功し、ログの送信先のAmazon S3 バケットとフォルダーにアクセスできる場合にのみ表示されます。

8. **配信オプション** メニューで、**ファイル名** フィールドを次のように編集します。

   a. **接頭辞**&#x200B;を変更します。 **ログファイルのプレフィックス**&#x200B;の下にあるLLM Optimizer設定ページから値をコピーします。

   ```
   {%Y}-{%m}-{%d}T{%H}:{%M}:{%S}.000
   ```

   b. **接尾辞**&#x200B;を変更します。 **ログファイルサフィックス**&#x200B;の下のLLM Optimizer設定ページから値をコピーします。

9. **プッシュ頻度**&#x200B;を変更します。 **Log Interval**&#x200B;の下のLLM Optimizer設定ページから値をコピーします。

   ![ ログ間隔](/help/overview/assets/log-forwarding/akamai/akamai-log-interval.png)

10. 「**次へ**」をクリックしてプロセスを完了します。

最終的な検証の前に、設定は次の例のようになります。

![設定の検証](/help/overview/assets/log-forwarding/akamai/akamai-validation.png)
