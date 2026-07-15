---
title: API キーの取得
description: LLM Optimizer から本番環境およびステージング環境の Edge Optimize API キーを取得する方法。
feature: Opportunities
autotag-review: '2026-07-15T18:05:12.505Z'
TQID: 'https://experienceleague.adobe.com/X3vIzxrlaqJ5Mx3K8rOpX2QqgGyxT6p8qfdLzTWC1gQ'
product_v2:
  - id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2:
  - id: d1956731-2adb-4bb7-8301-2b239254ac72
  - id: e1b649f0-0a61-46e4-9082-64d5cb2576c6
subfeature_v2:
  - id: d23587d6-14d6-4e3f-9ee1-cc18623832e1
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: bce87dde-a4ab-44c9-8a18-ad66e4ddb377
  - id: cdd65e7e-8839-44a2-bc21-0e03623b5dd1
  - id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 2705cf26faea9c09817bbdcec4b4c531552df7ba
workflow-type: tm+mt
source-wordcount: 337
ht-degree: 100%

---


# API キーの取得

CDN を設定する前に、LLM Optimizer UI から Edge Optimize API キーを取得します。 ライブトラフィックには、**本番環境**&#x200B;の API キーが必要です。 オプションで、**ステージング環境**&#x200B;の API キーを取得して、最初にステージング環境のホスト名でルーティングをテストすることもできます。

## 本番環境の API キー

1. LLM Optimizer で、**顧客設定**&#x200B;を開き、「**CDN 設定**」タブを選択します。

   ![顧客設定ダッシュボードに移動](/help/assets/optimize-at-edge/prereq-customer-config-nav.png)

2. 「**AI エージェントへ最適化をデプロイ**」 セクションを探します。 「**最適化エンジンを有効にする**」チェックボックスをオンにします。

   ![AI エージェントへ最適化をデプロイ - 保留中](/help/assets/optimize-at-edge/byocdn-deploy-optimizations-pending.png)

3. 確認ダイアログで、「**有効にする**」を選択します。

   ![最適化エンジンを有効にする確認ダイアログ](/help/assets/optimize-at-edge/byocdn-enable-optimization-engine-dialog.png)

4. 「**詳細を表示**」を選択します。 **最適化の詳細をデプロイ**&#x200B;ダイアログで、**本番環境の API キー**&#x200B;をコピーします（フィールドの横にある「**コピー**」を使用）。

   ![最適化の詳細をデプロイでの本番環境の API キー](/help/assets/optimize-at-edge/byocdn-production-api-key-details.png)

   >[!NOTE]
   >ダイアログに、設定が完了していないことが表示される場合があります。 ルーティングが検証されるまでは、この状態が続くことが想定されます。API キーをコピーしておくと、IT チームや CDN チームが設定を完了できます。

上記の手順について不明な点がある場合は、アドビのアカウントチームまたは `llmo-at-edge@adobe.com` にお問い合わせください。

## ステージング環境の API キー（オプション）

本番環境のルーティングを有効にする前に、下位環境でルーティングを検証するには、ステージング環境のホスト名を設定します。

**要件**

* ステージング環境のホスト名は、本番環境と&#x200B;**同じ登録可能なドメイン**&#x200B;上に指定する必要があります（例えば、本番環境が `https://www.example.com` の場合、ステージング環境のホスト名は `https://staging.example.com` と指定します）。
* サイトごとにステージング環境のドメインは **1 つ**&#x200B;のみです。 保存後は、アドビに連絡しない限り変更できません。

**手順**

1. LLM Optimizer で、**顧客設定**&#x200B;を開き、「**CDN 設定**」タブを選択します。
2. **AI エージェントへ最適化をデプロイ**&#x200B;で、「**ステージドメインを追加**」（またはステージング環境のドメインが既に設定されている場合は「**ステージドメイン**」）を選択します。
3. `https://` を含む完全なステージング環境の URL を入力し、「**ドメインを設定**」を選択します。
4. 確認ダイアログから&#x200B;**ステージング環境**&#x200B;の API キーをコピーします。

   ![ステージング環境のドメインの API キー](/help/assets/optimize-at-edge/byocdn-staging-domain-api-key.png)

ステージング環境の API キーを使用して、ステージング環境に同じルーティングルールをデプロイします。

ヘルプが必要な場合は、`llmo-at-edge@adobe.com` にお問い合わせください。

## 次の手順

API キーを取得したら、[CDN 設定ガイド](/help/dashboards/optimize-at-edge/overview.md#cdn-configuration-guides)に戻り、ルーティングを設定します。
