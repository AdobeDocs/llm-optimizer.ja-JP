---
source-git-commit: e9309dc8f8d1d81b953483f17dcb424e46d5cd3b
workflow-type: tm+mt
source-wordcount: '457'
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

## オプション：ステージングホスト名でのルーティングのテスト {#retrieve-staging-edge-optimize-api-key}

**オプション：ステージング ホスト名**&#x200B;でルーティングをテストします

実稼動用ルーティングを有効にする前に、下位の環境でルーティングを検証する場合は、ステージングホスト名を設定できます。

**要件**

* ステージング ホスト名は、実稼動環境と同じ&#x200B;**登録可能なドメイン**&#x200B;上にある必要があります（実稼動環境が`https://www.example.com`の場合は`https://staging.example.com`など）。
* サイトごとに&#x200B;**one**&#x200B;個のステージングドメインのみ。 保存した後は、Adobeに連絡せずに変更することはできません。

**ステージング API キーを取得**

1. **顧客設定**&#x200B;を開き、**CDN設定**&#x200B;を選択します。
2. 「**AI エージェントに最適化をデプロイ**」で、「**ステージドメインを追加**」（ステージングドメインが既に設定されている場合は「**ステージドメイン**」を選択）を選択します。
3. `https://`を含む完全なステージング URLを入力し、**ドメインを設定**&#x200B;を選択します。
4. 確認ダイアログから&#x200B;**ステージング** API キーをコピーします。

![ ステージング ドメイン API キー](/help/assets/optimize-at-edge/byocdn-staging-domain-api-key.png)

ステージング API キーを使用して、ステージング環境に同じルーティングルールをデプロイします。

**ステージング ボットのトラフィックをテスト**

`https://staging.example.com/page.html`を実際のステージング URLとパスに置き換えます。 **成功：**&#x200B;応答には`x-edgeoptimize-request-id` ヘッダーが含まれています。

サポートが必要な場合は、`llmo-at-edge@adobe.com`にお問い合わせください。

## LLM Optimizerでのルーティングステータスの確認 {#verify-routing-status-in-ui}

トラフィックルーティングのステータスは、LLM Optimizer UIでも確認できます。 **顧客設定**&#x200B;に移動し、**CDN設定** タブを選択します。

![AI エージェントへの最適化のデプロイ – 完了](/help/assets/optimize-at-edge/byocdn-CDN-traffic-routed-tick.png)

## ファイアウォールルールを使用したEdgeでの最適化の許可（オプション） {#waf-allowlist-setup}

CDNでWAFまたはBot Managerを使用している場合：

* WAFまたはBot Managerで`*AdobeEdgeOptimize/1.0*` ユーザーエージェントを許可リストに加えるして、Optimize at Edge サービスがオリジンコンテンツを取得できるようにします。
* ファイアウォールでユーザーエージェント以外の追加の検証が必要な場合は、秘密鍵（例：`openssl rand -hex 32`）を生成し、次の操作を行います。
   * ルーティングルールのシークレットを他の`x-edgeoptimize-*` ヘッダーと共に`x-edgeoptimize-fetcher-key`を追加します。
   * WAFまたはBot Manager ルールを追加して、`x-edgeoptimize-fetcher-key`が同じシークレットと一致するリクエストを許可します。
* Edgeで最適化すれば、このヘッダーをそのまま転送できます。ユーザーは鍵のライフサイクル全体を所有しています。

## 概要に戻る {#return-to-overview}

利用可能なオポチュニティ、自動最適化ワークフロー、FAQなど、Edgeでの最適化について詳しくは、[Edgeでの最適化の概要](/help/dashboards/optimize-at-edge/overview.md)に戻ります。
