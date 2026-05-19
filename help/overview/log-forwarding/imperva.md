---
title: ログ転送 - Imperva
description: LLM Optimizer でのエージェントトラフィックのデータ収集において、Imperva からアドビの S3 バケットに CDN ログを転送する方法について説明します。
feature: Agentic Traffic
autotag-review: '2026-05-15T17:52:22.260Z'
TQID: 'https://experienceleague.adobe.com/y2ticpRCNZjPYJ6wHg-V3QWxBnGF--mQfqGBYjVjKXY'
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2: id: d23587d6-14d6-4e3f-9ee1-cc18623832e1
topic_v2: id: d3cdead0-685a-4489-9250-4bb709942f66id: e1e0219c-f879-479f-8427-888ed2a6e9c2
source-git-commit: 564171851fdccee43afd233da143d66182464889
workflow-type: tm+mt
source-wordcount: 352
ht-degree: 100%

---


# ログ転送：Imperva {#log-forwarding-imperva}

このガイドでは、エージェントトラフィックのデータ収集を行うために、Imperva の CDN ログをアドビの S3 バケットに転送する方法について説明します。 LLM Optimizer にオンボードするには、LLM Optimizer CDN 設定ページを使用します。 オンボーディングプロセスが完了したら、このページに記載されている手順に従って、Imperva web コンソールからログ転送を設定します。

## 手順 1：LLM Optimizer でオンボード {#step-1}

LLM Optimizer ページ [https://llmo.now/](https://llmo.now/) で、次の手順に従います。

1. **設定**&#x200B;に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN 設定**」タブをクリックします。

   ![「CDN 設定」タブ](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)
1. 「**開始**」をクリックします。
1. 「**AI トラフィックインサイトをアクティブ化**」の横にある「**設定**」をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)
1. 「**Imperva（BYOCDN）**」を選択します。

   ![「Imperva」を選択](/help/overview/assets/log-forwarding/imperva/imperva-select.png)
1. 「**オンボード**」をクリックします。

## 手順 2：Imperva でログ転送を設定 {#step-2}

[Imperva コンソール](https://my.imperva.com)で、次の手順に従います。

>[!NOTE]
>
>ログは毎日送信する必要があります。

1. [https://my.imperva.com](https://my.imperva.com) で Imperva アカウントにログインします。

2. サイドバーで、**ログ**／**ログ設定**（または&#x200B;**ログ統合**）に移動します。

3. 接続タイプ（ログ宛先）として「**Amazon S3 ARN**」を選択します。

4. 以下を入力します。

   | フィールド | 説明 | メモ |
   |---|---|---|
   | **接続名** | 接続のわかりやすい名前（例：本番 S3 ログ）。 デフォルトの名前を変更できます。 | |
   | **パス** | ログファイルが保存されるフォルダーの場所。 `<Amazon S3 bucket name>/<log folder>` の形式を使用します。 例：`MyBucket/MyImpervaLogFolder`。 | `Amazon S3 bucket name` の&#x200B;**バケット名**&#x200B;は、LLM Optimizer 設定ページで指定されています。 ![バケット名](/help/overview/assets/log-forwarding/imperva/imperva-bucket-name.png)ログフォルダーの&#x200B;**パス**&#x200B;は、LLM Optimizer 設定ページで指定されています。 ![パス](/help/overview/assets/log-forwarding/imperva/imperva-path.png) |

5. 「**接続をテスト**」をクリックします。 Imperva は、テストファイル（実際のデータなし）を指定されたフォルダーに送信し、転送が完了したらそのファイルを削除するという、完全なテストを実行します。

   - **使用可能** - ストレージの詳細は有効です。この接続を使用するようにログを設定できます。
   - **未定義** - 必要な情報が欠落しているか、テストが失敗しました。

6. 「**保存**」をクリックして、設定を保存します。

7. ログオプション（ログタイプ、ログレベル、形式、圧縮）と&#x200B;**ログレベル**&#x200B;を設定します。 これらの値は、LLM Optimizer 設定ページから取得できます。

   | フィールド | メモ |
   |---|---|
   | ログ統合モード | ![ログ統合モード](/help/overview/assets/log-forwarding/imperva/imperva-log-integration-mode.png) |
   | 配信方法 | ![配信方法](/help/overview/assets/log-forwarding/imperva/imperva-delivery-method.png) |
   | ログタイプ | ![ログタイプ](/help/overview/assets/log-forwarding/imperva/imperva-log-types.png) |
   | ログレベル | ![ログレベル](/help/overview/assets/log-forwarding/imperva/imperva-log-level.png) |
   | 書式設定 | ![形式](/help/overview/assets/log-forwarding/imperva/imperva-format.png) |
   | ログを圧縮 | ![ログを圧縮](/help/overview/assets/log-forwarding/imperva/imperva-compress-logs.png) |
