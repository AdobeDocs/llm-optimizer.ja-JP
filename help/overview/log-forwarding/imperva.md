---
title: ログ転送 – Imperva
description: LLM Optimizerでアジェンティックトラフィックデータを収集するために Imperva からAdobeの S3 バケットに CDN ログを転送する方法を説明します。
feature: Agentic Traffic
source-git-commit: b590cd14ba7d64e56a6c972fd6090e2df9de58f6
workflow-type: tm+mt
source-wordcount: '352'
ht-degree: 5%

---


# ログ転送：Imperva {#log-forwarding-imperva}

このガイドでは、CDN ログを Imperva からAdobeの S3 バケットに転送してアジェンティックトラフィックデータを収集する方法について説明します。 LLM Optimizer CDN 設定ページを使用して、LLM Optimizerにオンボードします。 オンボーディングプロセスが完了したら、このページで説明する手順に従って、Imperva web コンソールからのログ転送を設定します。

## 手順 1:LLM Optimizerへのオンボード {#step-1}

LLM Optimizerページ [https://llmo.now/](https://llmo.now/) で、次の操作を行います。

1. **設定** に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN 設定**」タブをクリックします。

   ![CDN 設定タブ &#x200B;](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)
1. **開始** をクリックします。
1. **AI トラフィックインサイトをアクティブ化** の横にある **設定** をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)
1. 「**Imperva （BYOCDN）**」を選択します。

   ![&#x200B; インパーバを選択 &#x200B;](/help/overview/assets/log-forwarding/imperva/imperva-select.png)
1. **オンボード** をクリックします。

## 手順 2:Imperva でのログ転送の設定 {#step-2}

[Imperva コンソール &#x200B;](https://my.imperva.com) で：

>[!NOTE]
>
>ログは毎日送信する必要があります。

1. [https://my.imperva.com](https://my.imperva.com) で Imperva アカウントにログインします。

2. サイドバーで、**ログ**/**ログ設定** （または **ログ統合**）に移動します。

3. 接続タイプ（ログの送信先）として「**Amazon S3 ARN**」を選択します。

4. 以下を入力します。

   | フィールド | 説明 | メモ |
   |---|---|---|
   | **接続名** | 接続のわかりやすい名前（例：実稼動 S3 ログ）。 デフォルトの名前は変更できます。 | |
   | **パス** | ログファイルが保存されるフォルダーの場所。 形式 `<Amazon S3 bucket name>/<log folder>` を使用します。 例：`MyBucket/MyImpervaLogFolder`。 | `Amazon S3 bucket name` は、LLM Optimizer設定ページの **バケット名** です。![バケット名 &#x200B;](/help/overview/assets/log-forwarding/imperva/imperva-bucket-name.png) ログフォルダーは、LLM Optimizer設定ページの **パス** です。![パス &#x200B;](/help/overview/assets/log-forwarding/imperva/imperva-path.png) |

5. **接続をテスト** をクリックします。 Imperva は完全なテストを実行し、指定されたフォルダーにテストファイル（実際のデータなし）が送信され、転送が完了すると削除されます。

   - **使用可能** — ストレージの詳細は有効です。この接続を使用するようにログを構成できます。
   - **未定義** – 必要な詳細が見つからないか、テストが失敗しました。

6. **保存** をクリックして、設定を保存します。

7. ログオプション（ログタイプ、ログレベル、形式、圧縮）および **ログレベル** を設定します。 値は、LLM Optimizer設定ページから取得できます。

   | フィールド | メモ |
   |---|---|
   | ログ統合モード | ![&#x200B; ログ統合モード &#x200B;](/help/overview/assets/log-forwarding/imperva/imperva-log-integration-mode.png) |
   | 配信方法 | ![&#x200B; 配信方法 &#x200B;](/help/overview/assets/log-forwarding/imperva/imperva-delivery-method.png) |
   | ログタイプ | ![&#x200B; ログタイプ &#x200B;](/help/overview/assets/log-forwarding/imperva/imperva-log-types.png) |
   | ログレベル | ![&#x200B; ログレベル &#x200B;](/help/overview/assets/log-forwarding/imperva/imperva-log-level.png) |
   | 書式設定 | ![形式](/help/overview/assets/log-forwarding/imperva/imperva-format.png) |
   | ログを圧縮 | ![&#x200B; ログを圧縮 &#x200B;](/help/overview/assets/log-forwarding/imperva/imperva-compress-logs.png) |
