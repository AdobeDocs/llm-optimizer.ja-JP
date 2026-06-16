---
title: ログ転送 - Akamai
description: LLM Optimizer でのエージェントトラフィックのデータ収集において、Akamai からアドビの S3 バケットに CDN ログを転送する方法について説明します。
feature: Agentic Traffic
autotag-review: '2026-05-15T17:35:22.816Z'
TQID: 'https://experienceleague.adobe.com/cO-qqOveWFee1-QnVSlzmO-n383sptHl59Ni2qQcvAU'
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2: id: d23587d6-14d6-4e3f-9ee1-cc18623832e1
topic_v2: id: e1e0219c-f879-479f-8427-888ed2a6e9c2
source-git-commit: 76d1b915d41771580544d3d87732092cfecda6d5
workflow-type: tm+mt
source-wordcount: 612
ht-degree: 93%

---


# ログ転送：Akamai {#log-forwarding-akamai}

このページでは、エージェントトラフィックのデータ収集を行うために、Akamai の CDN ログをアドビの S3 バケットに転送する方法について説明します。 LLM Optimizer にオンボードするには、LLM Optimizer CDN 設定ページを使用します。 オンボーディングプロセスが完了したら、このページに記載されている手順に従って、Akamai コントロールパネルでログ転送を設定します。

## 手順 1：LLM Optimizer でオンボード {#step-1}

LLM Optimizer ページ [https://llmo.now/](https://llmo.now/) で、次の手順に従います。

1. **顧客設定ダッシュボード**&#x200B;に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN 設定**」タブをクリックします。

   ![「CDN 設定」タブ](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)

1. 「**開始**」をクリックします。

   <!--![Onboard CDN button](/help/overview/assets/log-forwarding/common/onboard-cdn-button.png)-->

1. 「**AI トラフィックインサイトをアクティブ化**」の横にある「**設定**」をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)

1. 「**Akamai（BYOCDN）**」を選択します。

   ![「Akamai」を選択](/help/overview/assets/log-forwarding/akamai/akamai-select.png)

1. 「**オンボード**」をクリックします。

   <!--![Onboard button](/help/overview/assets/log-forwarding/common/onboard-button.png)-->

## 手順 2：Akamai でストリームを作成 {#step-2}

Akamai コントロールパネル（[https://control.akamai.com/](https://control.akamai.com/)）で、Akamai 公式ドキュメントの手順に従って[ストリームを作成](https://techdocs.akamai.com/datastream2/docs/create-stream)します。

## 手順 3：データパラメーターを選択 {#step-3}

ストリームを作成した後、Akamai コントロールパネルで「**次へ**」をクリックして「**データセット**」タブに進みます。 Akamai 公式ドキュメントの手順に従って、[データパラメーター](https://techdocs.akamai.com/datastream2/docs/choose-data-parameters)を選択します。 LLM Optimizer 設定の次のフィールドが必要になります。

![LLMO 設定フィールド](/help/overview/assets/log-forwarding/akamai/akamai-llmo-config-fields.png)

マッピングは、次のように設定する必要があります。

* **ログ情報**
reqTimeSec -> リクエスト時間
* **地理データ**
country -> 国／地域
* **メッセージ交換データ**
reqHost -> リクエストホスト
reqPath -> リクエストパス
queryStr -> クエリ文字列（オプション）
reqMethod -> リクエストメソッド
ua -> ユーザーエージェント
statusCode -> HTTP ステータスコード
rspContentType -> 応答コンテンツタイプ
* **リクエストヘッダーデータ**
referer -> リファラー
* **ネットワークパフォーマンスデータ**
timeToFirstByte -> Time to first byte

>[!NOTE]
>
>`queryStr` パラメーターはオプションです。 クエリ文字列にPII情報が含まれている場合は、省略できます。

Akamai データセットフィールド（ID を含む）は次のとおりです。

1100, # reqTimeSec -> リクエスト時間
2012, # country -> 国／地域
1011, # reqHost -> リクエストホスト
1013, # reqPath -> リクエストパス
2009, # queryStr -> クエリ文字列（オプション）
1012, # reqMethod -> リクエストメソッド
1017, # ua -> ユーザーエージェント
1008, # statusCode -> HTTP ステータスコード
1032, # referer -> リファラー
1016, # rspContentType -> 応答コンテンツタイプ
2025  # timeToFirstByte -> Time to first byte

## 手順 4：宛先を設定 {#step-4}

データストリームを作成し、パラメーターを選択したら、宛先を設定する必要があります。 宛先を設定するには、次の手順に従います。

1. **宛先**&#x200B;で、「**S3**」を選択します。
2. **名前**&#x200B;で、宛先のわかりやすい説明を入力します。
3. **バケット**&#x200B;で、LLM Optimizer 設定ページから&#x200B;**バケット名**&#x200B;をコピーします。

   ![バケット名](/help/overview/assets/log-forwarding/common/bucket-name.png)

4. **フォルダーパス**&#x200B;で、LLM Optimizer 設定ページから&#x200B;**パス**&#x200B;をコピーします。

   ![ パス設定](/help/overview/assets/log-forwarding/akamai/akamai-path-config.png)

5. **地域**&#x200B;で、LLM Optimizer 設定ページから&#x200B;**地域**&#x200B;をコピーします。

   <!--![Region](/help/overview/assets/log-forwarding/common/region.png)-->

6. **アクセスキー ID** と&#x200B;**秘密鍵**&#x200B;で、LLM Optimizer 設定ページから両方の値をコピーします。

   ![アクセスキー](/help/overview/assets/log-forwarding/common/access-keys.png)

7. 「**検証して保存**」をクリックして、宛先への接続を検証し、指定した詳細を保存します。 この検証プロセスの一部として、システムは指定されたアクセスキー識別子と秘密鍵アクセスキーを使用して、ファイル名に `Akamai_access_verification_[TimeStamp].txt` 形式のタイムスタンプを含む検証ファイルを S3 フォルダーに作成します。 このファイルは、検証プロセスが成功し、ログの送信先となる Amazon S3 バケットとフォルダーへのアクセス権がある場合にのみ表示されます。

8. **配信オプション**&#x200B;メニューで、「**ファイル名**」フィールドを次のように編集します。

   a. **接頭辞**&#x200B;を変更します。 LLM Optimizer 設定ページの&#x200B;**ログファイル接頭辞**&#x200B;から値をコピーします。

   ```
   {%Y}-{%m}-{%d}T{%H}:{%M}:{%S}.000
   ```

   b. **接尾辞**&#x200B;を変更します。 LLM Optimizer 設定ページの&#x200B;**ログファイル接尾辞**&#x200B;から値をコピーします。

9. **プッシュ頻度**&#x200B;を変更します。 LLM Optimizer 設定ページの&#x200B;**ログ間隔**&#x200B;から値をコピーします。

   ![ログ間隔](/help/overview/assets/log-forwarding/akamai/akamai-log-interval.png)

10. 「**次へ**」をクリックして、プロセスを完了します。

最終検証の前に、設定は次の例のようになります。

![設定の検証](/help/overview/assets/log-forwarding/akamai/akamai-validation.png)
