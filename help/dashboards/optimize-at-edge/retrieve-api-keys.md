---
title: API キーの取得
description: LLM Optimizerから実稼動環境とステージング Edge Optimize API キーを取得する方法。
feature: Opportunities
source-git-commit: 3b6dc163f4488a22937916beb6778de4abc5a20c
workflow-type: tm+mt
source-wordcount: '337'
ht-degree: 0%

---


# API キーの取得

CDNを設定する前に、LLM Optimizer UIからEdge Optimize API キーを取得します。 ライブトラフィックには&#x200B;**本番** API キーが必要です。 オプションで、**ステージング** API キーを取得して、最初にステージング ホスト名でルーティングをテストすることもできます。

## 実稼動API キー

1. LLM Optimizerで、**顧客設定**&#x200B;を開き、**CDN設定** タブを選択します。

   ![顧客設定に移動](/help/assets/optimize-at-edge/prereq-customer-config-nav.png)

2. 「**AI エージェントに最適化をデプロイ**」セクションを見つけます。 「**最適化エンジンを有効にする**」チェックボックスをオンにします。

   ![AI エージェントへの最適化のデプロイ – 保留中](/help/assets/optimize-at-edge/byocdn-deploy-optimizations-pending.png)

3. 確認ダイアログで、**有効**&#x200B;を選択します。

   ![最適化エンジン確認ダイアログを有効にする](/help/assets/optimize-at-edge/byocdn-enable-optimization-engine-dialog.png)

4. **詳細を表示**&#x200B;を選択します。 **最適化の詳細をデプロイ** ダイアログで、**実稼動API キー**&#x200B;をコピーします（フィールドの横に&#x200B;**コピー**&#x200B;を使用）。

   デプロイ最適化の詳細![実稼動API キー](/help/assets/optimize-at-edge/byocdn-production-api-key-details.png)

   >[!NOTE]
   >設定が完了していないことがダイアログに表示される場合があります。 これは、ルーティングが検証されるまで想定されます。引き続きAPI キーをコピーして、IT部門またはCDN部門が設定を完了できるようにすることができます。

上記の手順についてサポートが必要な場合は、Adobe アカウントチームまたは`llmo-at-edge@adobe.com`にお問い合わせください。

## ステージング API キー（オプション）

実稼動ルーティングを有効にする前に、下位の環境でルーティングを検証するには、ステージングホスト名を設定します。

**要件**

* ステージング ホスト名は、実稼動環境と同じ&#x200B;**登録可能なドメイン**&#x200B;上にある必要があります（実稼動環境が`https://www.example.com`の場合は`https://staging.example.com`など）。
* サイトごとに&#x200B;**one**&#x200B;個のステージングドメインのみ。 保存した後は、Adobeに連絡せずに変更することはできません。

**手順**

1. LLM Optimizerで、**顧客設定**&#x200B;を開き、**CDN設定** タブを選択します。
2. 「**AI エージェントに最適化をデプロイ**」で、「**ステージドメインを追加**」（ステージングドメインが既に設定されている場合は「**ステージドメイン**」を選択）を選択します。
3. `https://`を含む完全なステージング URLを入力し、**ドメインを設定**&#x200B;を選択します。
4. 確認ダイアログから&#x200B;**ステージング** API キーをコピーします。

   ![ ステージング ドメイン API キー](/help/assets/optimize-at-edge/byocdn-staging-domain-api-key.png)

ステージング API キーを使用して、ステージング環境に同じルーティングルールをデプロイします。

サポートが必要な場合は、`llmo-at-edge@adobe.com`にお問い合わせください。

## 次の手順

API キーを取得したら、[CDN セットアップガイド ](/help/dashboards/optimize-at-edge/overview.md#cdn-configuration-guides)に戻ってルーティングを設定します。
