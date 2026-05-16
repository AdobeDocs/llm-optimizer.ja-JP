---
title: ログ転送 – Imperva
description: ImpervaからAdobeにCDN ログを転送する方法を説明しますS3 バケット LLM Optimizerのエージェンティックトラフィックデータ収集のために。
feature: Agentic Traffic
autotag-review: '2026-05-15T17:52:22.260Z'
TQID: 'https://experienceleague.adobe.com/y2ticpRCNZjPYJ6wHg-V3QWxBnGF--mQfqGBYjVjKXY'
product_v2:
  - id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2:
  - id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2:
  - id: d23587d6-14d6-4e3f-9ee1-cc18623832e1
topic_v2:
  - id: d3cdead0-685a-4489-9250-4bb709942f66
  - id: e1e0219c-f879-479f-8427-888ed2a6e9c2
source-git-commit: 564171851fdccee43afd233da143d66182464889
workflow-type: tm+mt
source-wordcount: 352
ht-degree: 5%

---


# ログ転送：Imperva {#log-forwarding-imperva}

このガイドでは、エージェント型トラフィックデータ収集用にImpervaからAdobeのS3 バケットにCDN ログを転送する方法について説明します。 LLM Optimizer CDN設定ページを使用して、LLM Optimizerにオンボーディングします。 オンボーディングプロセスが完了したら、このページに記載されている手順に従って、Imperva web コンソールからログ転送を設定します。

## ステップ 1: LLM Optimizerでのオンボーディング {#step-1}

LLM Optimizer ページ [https://llmo.now/](https://llmo.now/)で、次の操作を行います。

1. **設定**&#x200B;に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN設定**」タブをクリックします。

   ![CDN設定タブ &#x200B;](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)
1. 「**基本を学ぶ**」をクリックします。
1. 「**AI トラフィックインサイトをアクティブ化**」の横にある「**設定**」をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)
1. **Imperva （BYOCDN）**&#x200B;を選択します。

   ![Impervaを選択](/help/overview/assets/log-forwarding/imperva/imperva-select.png)
1. 「**オンボード**」をクリックします。

## 手順2:Impervaでのログ転送の設定 {#step-2}

[Imperva コンソール &#x200B;](https://my.imperva.com)で：

>[!NOTE]
>
>ログは毎日送信されます。

1. [https://my.imperva.com](https://my.imperva.com)でImperva アカウントにログインします。

2. サイドバーで、**Logs** > **Log Setup** （または&#x200B;**Log Integration**）に移動します。

3. 接続タイプ（ログの宛先）として&#x200B;**Amazon S3 ARN**&#x200B;を選択します。

4. 以下を入力します。

   | フィールド | 説明 | メモ |
   |---|---|---|
   | **接続名** | 接続のわかりやすい名前（実稼動S3 ログなど）。 デフォルトの名前を変更できます。 | |
   | **パス** | ログファイルが保存されるフォルダーの場所。 形式`<Amazon S3 bucket name>/<log folder>`を使用します。 例：`MyBucket/MyImpervaLogFolder`。 | `Amazon S3 bucket name`は、LLM Optimizer設定ページの&#x200B;**バケット名**&#x200B;です。 ![&#x200B; バケット名](/help/overview/assets/log-forwarding/imperva/imperva-bucket-name.png) ログフォルダーは、LLM Optimizer設定ページの&#x200B;**パス**&#x200B;です。 ![パス](/help/overview/assets/log-forwarding/imperva/imperva-path.png) |

5. 「**接続をテスト**」をクリックします。 Impervaは完全なテストを実行し、テストファイル（実際のデータなし）が指定されたフォルダーに送信され、転送が完了したときに削除されます。

   - **Available** — ストレージの詳細は有効です。この接続を使用するようにログを設定できます。
   - **未定義** – 必要な詳細がないか、テストに失敗しました。

6. **保存**&#x200B;をクリックして、設定を保存します。

7. ログオプション（ログタイプ、ログレベル、形式、圧縮）と&#x200B;**ログレベル**&#x200B;を設定します。 値は、LLM Optimizer設定ページから取得できます。

   | フィールド | メモ |
   |---|---|
   | ログ統合モード | ![&#x200B; ログ統合モード &#x200B;](/help/overview/assets/log-forwarding/imperva/imperva-log-integration-mode.png) |
   | 配信方法 | ![配信方法](/help/overview/assets/log-forwarding/imperva/imperva-delivery-method.png) |
   | ログタイプ | ![&#x200B; ログの種類](/help/overview/assets/log-forwarding/imperva/imperva-log-types.png) |
   | ログレベル | ![&#x200B; ログレベル &#x200B;](/help/overview/assets/log-forwarding/imperva/imperva-log-level.png) |
   | 書式設定 | ![形式](/help/overview/assets/log-forwarding/imperva/imperva-format.png) |
   | ログを圧縮 | ![&#x200B; ログを圧縮](/help/overview/assets/log-forwarding/imperva/imperva-compress-logs.png) |
