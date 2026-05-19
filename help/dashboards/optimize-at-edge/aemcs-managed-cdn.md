---
title: Edge での最適化 - AEM Cloud Service の管理による CDN（Fastly）
description: LLM Optimizer の Edge での最適化を行うのに AEM Cloud Service の管理による CDN（Fastly）を設定する方法について説明します。
feature: Opportunities
autotag-review: '2026-05-15T17:31:38.650Z'
TQID: 'https://experienceleague.adobe.com/qrCODY3Qg6dDd9QRcaYGdN4YVCgKAsl56UTAVZpsIwE'
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: d1956731-2adb-4bb7-8301-2b239254ac72id: e0828736-236a-487b-a478-5a635455eadc
subfeature_v2: id: d23587d6-14d6-4e3f-9ee1-cc18623832e1id: e06fae5f-830b-4222-a469-b5e148d36465
topic_v2: id: cdd65e7e-8839-44a2-bc21-0e03623b5dd1
source-git-commit: 7a92587197cf6a9eec6b01bd4eaeeaf1194d3088
workflow-type: tm+mt
source-wordcount: 836
ht-degree: 100%

---


# AEM Cloud Service の管理による CDN（Fastly）

この設定では、エージェントトラフィック（AI ボットおよび LLM ユーザーエージェントからのリクエスト）を Edge での最適化バックエンドサービス（`live.edgeoptimize.net`）にルーティングします。 人間の訪問者と SEO ボットは、通常どおりオリジンから引き続き提供されます。 設定をテストするには、設定が完了したら、応答のヘッダー `x-edgeoptimize-request-id` を確認します。

## 前提条件

この機能にアクセスするには：

- 有料のお客様は、**Adobe LLM Optimizer ユーザー** IMS 製品プロファイルへのアクセス権が必要です。 アクセス権をリクエストするには、組織の管理者にお問い合わせください。
  ![製品プロファイルにユーザーを追加](/help/assets/optimize-at-edge/cs-fastly-user-product-profiles.png)
- 体験版のユーザーは、**LLMO 管理者** IMS グループに参加している必要があります。 グループが存在しない場合は、組織の管理者がグループを作成して追加できます。
  ![LLMO 管理者 IMS グループを作成](/help/assets/optimize-at-edge/cs-fastly-create-ims-group.png)

>[!NOTE]
> この機能は、Safari または匿名／プライベート参照モードではサポートされていません。

## ルーティングを有効にする手順

Edge での最適化へのエージェントトラフィックのルーティングを開始するには：

1. LLM Optimizer で、**顧客設定**&#x200B;を開き、「**CDN 設定**」タブを選択します。

   ![顧客設定ダッシュボードに移動](/help/assets/optimize-at-edge/cs-fastly-prereq-customer-config-nav.png)

2. 「**AI エージェントへ最適化をデプロイ**」 セクションを探します。 「**有効**」ボタンをクリックします。

   ![AI エージェントへ最適化をデプロイ - 保留中](/help/assets/optimize-at-edge/cs-fastly-enable-button.png)

3. 確認ダイアログで「**有効**」を選択して、ルーティングを有効にすることを確認します。 エラーが表示された場合は、[トラブルシューティング](#troubleshooting)の節を参照して解決してください。

   ![最適化エンジンを有効にする確認ダイアログ](/help/assets/optimize-at-edge/cs-fastly-enable-dialog.png)

4. 確認が完了すると、ルーティングの完了には数分かかります。

   ![ルーティング中](/help/assets/optimize-at-edge/cs-fastly-enable-button-clicked-routing-in-progress.png)

   ルーティングが完了したことを確認するには、5 分後にページを再読み込みします。 ルーティングが設定され、アクティブになると、ステータスが&#x200B;**完了**&#x200B;に更新され、ルーティングが有効になっていることを示す緑色のチェックマークが表示されます。 ユーザー側でこれ以上のアクションを行う必要はありません。

   ![AI エージェントへ最適化をデプロイ - 完了](/help/assets/optimize-at-edge/cs-fastly-disable-button.png)

   ルーティングをいつでも無効にするには、「**CDN 設定**」タブの「**AI エージェントへ最適化をデプロイ**」セクションに戻り、「**無効**」をクリックします。

また、上記の手順について不明な点がある場合は、アドビのアカウントチームまたは `llmo-at-edge@adobe.com` にお問い合わせください。

## トラブルシューティング

ルーティングの有効化または無効化中にエラーが表示された場合、次のようになります。

![確認ダイアログエラー](/help/assets/optimize-at-edge/cs-fastly-confirmation-dialog-error.png)

以下のリストを使用して、エラーを特定し、指示に従います。

1. **ユーザーが LLMO 製品へのアクセス権を持っていない**

   **原因：**&#x200B;ユーザーアカウントの Adobe IMS プロファイルには、LLM Optimizer の製品コンテキストがありません。 これは、有料のお客様が CDN ルーティングを設定するために必要です。

   **レコメンデーション：**&#x200B;組織管理者により、Adobe Admin Console で **Adobe LLM Optimizer ユーザー**&#x200B;製品プロファイルが割り当てられていることを確認します。

2. **LLMO 管理者グループのメンバーのみが CDN ルーティングを設定できる**

   **原因：**&#x200B;アカウントが **LLMO 管理者** IMS グループのメンバーではありません。 これは、体験版のお客様が CDN ルーティングを設定するために必要です。

   **レコメンデーション：**&#x200B;組織管理者により、Adobe Admin Console の **LLMO 管理者** IMS グループに追加されていることを確認します。

3. **リクエストされた CDN タイプ aem-cs-fastly が、このドメインで検出された CDN と一致しない**

   **原因：**&#x200B;これは、サイトで検出された CDN タイプが *AEM Cloud Service Managed CDN（Fastly）*&#x200B;ではないことを示しています。

   **レコメンデーション：**&#x200B;サイトが AEM Cloud Service Managed CDN（Fastly）を通じて配信されていることを確認します。

4. **サイトプローブエラー**

   **原因：** LLM Optimizer がルーティング設定中にサイトに到達できませんでした。 これは、サイトがダウンしている、未到達、リクエストがタイムアウトした場合に発生することがあります。

   **レコメンデーション：**&#x200B;サイトが一般にアクセス可能で、有効な応答を返していることを確認してから、もう一度試してください。

5. **サイトがルーティングプローブに対して有効な応答を返さなかった**

   **原因：**&#x200B;設定中にプローブを実行すると、サイトが予期しない HTTP ステータス（2xx または 301 以外）が返されました。

   **レコメンデーション：** LLM Optimizer に登録されている基本 URL に対して、サイトが正常な応答（2xx）を返していることを確認してから、もう一度試してください。

6. **アップストリーム IMS サービスでの認証に失敗した**

   **原因：**&#x200B;セッションの有効期限が切れたか、ルーティングリクエスト中に Adobe IMS との認証に問題が発生した可能性があります。

   **レコメンデーション：** LLM Optimizer からログアウトして再度ログインし、ルーティングをもう一度有効にしてみてください。

問題が解決しない場合は、アドビのアカウントチームまたは `llmo-at-edge@adobe.com` にお問い合わせください。

## （オプション）設定の確認

ルーティング設定が完了したら、オプションで、AI ボットトラフィックが Edge での最適化にルーティングされていることと、人間のトラフィックに影響がないことを確認できます。

1. **ボットトラフィックをテスト（最適化する必要があります）**

   エージェント型ユーザーエージェントを使用して、AI ボットリクエストをシミュレートします。

   ```
   curl -svo /dev/null https://www.example.com/page.html \
     --header "user-agent: chatgpt-user"
   ```

   正常な応答には、リクエストが Edge での最適化を経由してルーティングされたことを確認する `x-edgeoptimize-request-id` ヘッダーが含まれます。

   ```
   < HTTP/2 200
   < x-edgeoptimize-request-id: 50fce12d-0519-4fc6-af78-d928785c1b85
   ```

2. **人間のトラフィックをテスト（影響を受けません）**

   通常の人間によるブラウザーリクエストをシミュレートします。

   ```
   curl -svo /dev/null https://www.example.com/page.html \
     --header "user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36"
   ```

   応答には、`x-edgeoptimize-request-id` ヘッダーを含めないでください。 ページのコンテンツと応答時間は、Edge での最適化を有効にする前と同じ状態を維持する必要があります。

3. **2 つのシナリオを区別する方法**

   | ヘッダー | ボットトラフィック（最適化） | 人間のトラフィック（影響を受けない） |
   |---|---|---|
   | `x-edgeoptimize-request-id` | 存在 - 一意のリクエスト ID が含まれます | 不在 |
   | `x-edgeoptimize-fo` | フェイルオーバーが発生した場合のみ存在（値：`1`） | 不在 |

4. **LLM Optimizer のルーティングステータスを確認**

   LLM Optimizer UI でルーティングを確認することもできます。 **顧客設定**&#x200B;を開き、「**CDN 設定**」タブを選択します。 ルーティングがアクティブな場合、「**AI エージェントへ最適化をデプロイ**」セクションに「**完了**」と表示されます。

   ![AI エージェントへ最適化をデプロイ - 完了](/help/assets/optimize-at-edge/cs-fastly-disable-button.png)

{{return-to-overview}}
