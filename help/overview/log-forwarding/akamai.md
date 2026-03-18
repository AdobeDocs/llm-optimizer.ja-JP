---
title: ログ転送 – Akamai
description: LLM Optimizerでアジェンティックトラフィックデータを収集するために Akamai からAdobeの S3 バケットに CDN ログを転送する方法を説明します。
feature: Agentic Traffic
source-git-commit: b590cd14ba7d64e56a6c972fd6090e2df9de58f6
workflow-type: tm+mt
source-wordcount: '595'
ht-degree: 0%

---


# ログ転送：Akamai {#log-forwarding-akamai}

Akamai からAdobeの S3 バケットに CDN ログを転送してアジェンティックトラフィックデータを収集する方法を説明します。 LLM Optimizer CDN 設定ページを使用して、LLM Optimizerにオンボードします。 オンボーディングプロセスが完了したら、このページで説明する手順に従って、Akamai Campaign コントロールパネルでログ転送を設定します。

## 手順 1:LLM Optimizerへのオンボード {#step-1}

LLM Optimizerページ [https://llmo.now/](https://llmo.now/) で、次の操作を行います。

1. **顧客設定ダッシュボード** に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN 設定**」タブをクリックします。

   ![CDN 設定タブ ](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)

1. **開始** をクリックします。

   <!--![Onboard CDN button](/help/overview/assets/log-forwarding/common/onboard-cdn-button.png)-->

1. **AI トラフィックインサイトをアクティブ化** の横にある **設定** をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)

1. 「**Akamai （BYOCDN）**」を選択します。

   ![Akamai を選択 ](/help/overview/assets/log-forwarding/akamai/akamai-select.png)

1. **オンボード** をクリックします。

   <!--![Onboard button](/help/overview/assets/log-forwarding/common/onboard-button.png)-->

## 手順 2:Akamai でのストリームの作成 {#step-2}

Akamai コントロールパネル [https://control.akamai.com/で ](https://control.akamai.com/) 公式の Akamai ドキュメントの手順に従って [ ストリームを作成 ](https://techdocs.akamai.com/datastream2/docs/create-stream) します。

## 手順 3：データパラメーターの選択 {#step-3}

ストリームを作成した後、Akamai コントロールパネルで「次へ」をクリックし、「**データセット**」タブに進みます。 Akamai の公式ドキュメントの手順に従って、[ データパラメーター ](https://techdocs.akamai.com/datastream2/docs/choose-data-parameters) を選択します。 LLM Optimizer設定の次のフィールドが必要になります。

![LLMO 設定フィールド ](/help/overview/assets/log-forwarding/akamai/akamai-llmo-config-fields.png)

マッピングは次のようになります。

* **ログ情報**
reqTimeSec -> リクエスト時間
* **地域データ**
国 – > 国/地域
* **メッセージ交換データ**
reqHost -> リクエストホスト
reqPath -> リクエストパス
queryStr -> クエリ文字列
reqMethod -> リクエストメソッド
ua -> User-Agent
statusCode -> HTTP ステータスコード
rspContentType -> 応答 Content-Type
* **リクエストヘッダーデータ**
リファラー – > リファラー
* **ネットワークパフォーマンスデータ**
timeToFirstByte -> 最初のバイトまでの時間

Akamai データセットのフィールド（ID を含む）は、次のとおりです。

1100, # reqTimeSec -> リクエスト時間
2012 年、#国 – > 国/地域
1011, # reqHost -> リクエスト・ホスト
1013, # reqPath -> リクエストパス
2009, # queryStr -> クエリ文字列
1012, # reqMethod -> リクエストメソッド
1017, # ua -> User-Agent
1008, # statusCode -> HTTP ステータス コード
1032, # リファラー – > リファラー
1016, # rspContentType -> 応答 Content-Type
2025 # timeToFirstByte -> Time to first byte

## 手順 4：宛先の設定 {#step-4}

データストリームを作成し、パラメーターを選択した後、宛先を設定する必要があります。 宛先を設定するには、次の手順に従います。

1. **宛先** で、「**S3**」を選択します。
2. **名前** に、人間が判読できる宛先の説明を入力します。
3. **バケット** で、**バケット名** をLLM Optimizer設定ページからコピーします。

   ![ バケット名 ](/help/overview/assets/log-forwarding/common/bucket-name.png)

4. **フォルダーパス** で、LLM Optimizer設定ページから **パス** をコピーします。

   ![ パス設定 ](/help/overview/assets/log-forwarding/akamai/akamai-path-config.png)

5. **Region** で、LLM Optimizer設定ページから **Region** をコピーします。

   <!--![Region](/help/overview/assets/log-forwarding/common/region.png)-->

6. **アクセスキー ID** および **秘密鍵** で、LLM Optimizer設定ページから両方の値をコピーします。

   ![ アクセスキー ](/help/overview/assets/log-forwarding/common/access-keys.png)

7. 「**検証して保存**」をクリックして、宛先への接続を検証し、指定した詳細を保存します。 この検証プロセスの一環として、システムは、指定されたアクセスキー識別子と秘密アクセスキーを使用して、`Akamai_access_verification_[TimeStamp].txt` 形式のファイル名にタイムスタンプを含む検証ファイルを S3 フォルダーに作成します。 このファイルが表示されるのは、検証プロセスが正常に完了し、ログの送信先のAmazon S3 バケットおよびフォルダーにアクセスできる場合のみです。

8. **配信オプション** メニューで、「**ファイル名**」フィールドを次のように編集します。

   a. **prefix** を変更します。 **ログファイルのプレフィックス** の下のLLM Optimizer設定ページから値をコピーします。

   ```
   {%Y}-{%m}-{%d}T{%H}:{%M}:{%S}.000
   ```

   b. **サフィックス** を変更します。 「**ログファイルのサフィックス** の下のLLM Optimizer設定ページから値をコピーします。

9. **プッシュ頻度** を変更します。 **Log Interval** の下のLLM Optimizer設定ページから値をコピーします。

   ![ ログ間隔 ](/help/overview/assets/log-forwarding/akamai/akamai-log-interval.png)

10. 「**次へ**」をクリックして、プロセスを完了します。

最終的な検証を行う前に、設定は次の例のようになります。

![ 設定の検証 ](/help/overview/assets/log-forwarding/akamai/akamai-validation.png)
