---
source-git-commit: da789100d814004687de2f46e18a295671dec4b8
workflow-type: tm+mt
source-wordcount: '363'
ht-degree: 0%

---
# スニペット

## API キー取得ステップ {#retrieve-byocdn-api-key}

**実稼動のEdge Optimize API キーを取得する手順：**

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

## ステージング ドメイン API キー（オプション） {#retrieve-staging-edge-optimize-api-key}

実稼動トラフィックがルーティングルールを使用する前に、より低い環境でEdgeでOptimizeをテストする場合は、ステージングホスト名を使用します。

**前提条件**

* ステージング ホスト名は、実稼動サイトと&#x200B;**同じ登録可能ドメイン**&#x200B;に属している必要があります（実稼動が`https://www.example.com`の場合は`https://staging.example.com`など）。
* サイトに対して設定できるのは&#x200B;**one** ステージング ドメインのみです。 保存した後は、支援なしでは変更できません。

**手順**

1. LLM Optimizerで、**顧客設定**&#x200B;を開き、**CDN設定** タブを選択します。

2. 「**AI エージェントに最適化をデプロイ**」セクションで、「**ステージドメインを追加**」（ステージングドメインが既に設定されている場合は&#x200B;**ステージドメイン**）を選択します。

3. **ステージングドメイン** ダイアログで、`https://`を含む完全なステージング URLを入力し、**ドメインを設定**&#x200B;を選択します。

   ![ ステージドメイン入力ダイアログ ](/help/assets/optimize-at-edge/byocdn-staging-domain-input.png)

4. 次のプロンプトでドメインを確認します。 ワークフローが完了すると、設定されたドメインとその&#x200B;**API キー**&#x200B;が&#x200B;**ステージドメイン** ダイアログに表示されます。 「**コピー**」を選択して、ステージング API キーをコピーします。

   ![ ステージング ドメイン API キー](/help/assets/optimize-at-edge/byocdn-staging-domain-api-key.png)

サポートが必要な場合は、`llmo-at-edge@adobe.com`にお問い合わせください。

## LLM Optimizerでのルーティングステータスの確認 {#verify-routing-status-in-ui}

トラフィックルーティングのステータスは、LLM Optimizer UIでも確認できます。 **顧客設定**&#x200B;に移動し、**CDN設定** タブを選択します。

![AI エージェントへの最適化のデプロイ – 完了](/help/assets/optimize-at-edge/byocdn-CDN-traffic-routed-tick.png)

## 概要に戻る {#return-to-overview}

利用可能なオポチュニティ、自動最適化ワークフロー、FAQなど、Edgeでの最適化について詳しくは、[Edgeでの最適化の概要](/help/dashboards/optimize-at-edge/overview.md)に戻ります。
