---
title: Edgeでの最適化 – AEM Cloud Service Managed CDN （Fastly）
description: LLM OptimizerのEdgeで、AEM Cloud Service Managed CDN （Fastly） for Optimizeを設定する方法について説明します。
feature: Opportunities
source-git-commit: 184d6008c2579014c6ff453e8bfff4bb898f4b82
workflow-type: tm+mt
source-wordcount: '836'
ht-degree: 0%

---


# AEM Cloud Service Managed CDN （Fastly）

この設定により、エージェント型トラフィック（AI ボットおよびLLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 通常どおり、人間の訪問者とSEO ボットは、元のページから引き続き提供されます。 設定をテストするには、設定が完了した後、応答のヘッダー`x-edgeoptimize-request-id`を確認します。

## 前提条件

この機能にアクセスするには：

- 有料のお客様は、**Adobe LLM Optimizer Users** IMS Product Profileにアクセスできる必要があります。 アクセスをリクエストするには、組織の管理者にお問い合わせください。
  ![製品プロファイルにユーザーを追加](/help/assets/optimize-at-edge/cs-fastly-user-product-profiles.png)
- 体験版のお客様は、**LMO Admin** IMS グループに参加している必要があります。 グループが存在しない場合は、組織の管理者がグループを作成して追加できます。
  ![LLMO管理者IMS グループの作成](/help/assets/optimize-at-edge/cs-fastly-create-ims-group.png)

>[!NOTE]
> この機能は、Safariまたはシークレット/プライベートブラウジングモードではサポートされていません。

## ルーティングを有効にする手順

エージェント型トラフィックのEdge Optimizeへのルーティングを開始するには：

1. LLM Optimizerで、**顧客設定**&#x200B;を開き、**CDN設定** タブを選択します。

   ![顧客設定に移動](/help/assets/optimize-at-edge/cs-fastly-prereq-customer-config-nav.png)

2. 「**AI エージェントに最適化をデプロイ**」セクションを見つけます。 「**有効化**」ボタンをクリックします。

   ![AI エージェントへの最適化のデプロイ – 保留中](/help/assets/optimize-at-edge/cs-fastly-enable-button.png)

3. 確認ダイアログで「**有効にする**」を選択し、ルーティングを有効にすることを確認します。 エラーが発生した場合は、「[&#x200B; トラブルシューティング &#x200B;](#troubleshooting)」セクションを参照して解決してください。

   ![最適化エンジン確認ダイアログを有効にする](/help/assets/optimize-at-edge/cs-fastly-enable-dialog.png)

4. 確認が完了すると、ルーティングが完了するまでに数分かかります。

   ![進行中のルーティング &#x200B;](/help/assets/optimize-at-edge/cs-fastly-enable-button-clicked-routing-in-progress.png)

   5分後にページをリロードし、ルーティングが完了したことを確認します。 ルーティングが設定され、アクティブになると、ルーティングが有効であることを確認する緑色のチェックマークが付いて、ステータスが&#x200B;**完了**&#x200B;に更新されます。 ユーザー側でこれ以上の操作は必要ありません。

   ![AI エージェントへの最適化のデプロイ – 完了](/help/assets/optimize-at-edge/cs-fastly-disable-button.png)

   ルーティングを無効にするには、**CDN設定** タブの「**AI エージェントに最適化をデプロイ**」セクションに戻り、**無効化**」をクリックします。

上記の手順についてサポートが必要な場合は、Adobe アカウントチームまたは`llmo-at-edge@adobe.com`にお問い合わせください。

## トラブルシューティング

ルーティングを有効または無効にする際にエラーが発生した場合は、次のようになります。

![確認ダイアログエラー](/help/assets/optimize-at-edge/cs-fastly-confirmation-dialog-error.png)

以下のリストを使用して、エラーを特定し、指示に従います。

1. **ユーザーはLLMO製品にアクセスできません**

   **原因：** Adobe IMSアカウントには、ユーザープロファイルにLLM Optimizer製品コンテキストがありません。 これは、有料顧客がCDN ルーティングを設定するために必要です。

   **推奨事項：** Adobe Admin Consoleの&#x200B;**Adobe LLM Optimizer Users**&#x200B;製品プロファイルが組織管理者によって割り当てられていることを確認します。

2. **CDN ルーティングを設定できるのはLLMO管理者グループ メンバーのみです**

   **原因：**&#x200B;お客様のアカウントは&#x200B;**LLMO Admin** IMS グループのメンバーではありません。 これは、体験版のお客様がCDN ルーティングを設定するために必要です。

   **推奨事項：**&#x200B;組織管理者がAdobe Admin Consoleの&#x200B;**LLMO管理者** IMS グループに追加したことを確認します。

3. **要求されたCDN タイプ aem-cs-fastlyがこのドメインで検出されたCDNと一致しません**

   **原因：**&#x200B;これは、サイトで検出されたCDN タイプが&#x200B;*AEM Cloud Service Managed CDN （Fastly）*&#x200B;でないことを示します。

   **推奨事項：**&#x200B;お客様のサイトがAEM Cloud Service Managed CDN （Fastly）を通じて提供されていることを確認します。

4. **サイトの調査エラー**

   **原因：** ルーティング設定中にLLM Optimizerがサイトにアクセスできませんでした。 これは、サイトがダウンしているか、到達できないか、リクエストがタイムアウトした場合に発生する可能性があります。

   **推奨事項：**&#x200B;あなたのサイトが一般にアクセス可能であることを確認し、有効な応答を返してから、もう一度試してください。

5. **サイトはルーティング プローブに対して有効な応答を返しませんでした**

   **原因：** セットアップ中にプローブを実行すると、サイトが予期しないHTTP ステータスを返しました（2xxまたは301ではありません）。

   **推奨事項：** LLM Optimizerに登録されている基本URLに対して、サイトが正常な応答（2xx）を返していることを確認してから、再試行してください。

6. **アップストリーム IMS サービスで認証に失敗しました**

   **原因：** セッションが期限切れになっているか、ルーティング リクエスト中にAdobe IMSによる認証中に問題が発生した可能性があります。

   **推奨事項：** LLM Optimizerからログアウトして再度ログインし、ルーティングを再度有効にしてみてください。

問題が解決しない場合は、Adobe アカウントチームまたは`llmo-at-edge@adobe.com`にお問い合わせください。

## （オプション）設定の確認

ルーティング設定が完了したら、オプションで、AI ボットトラフィックがEdge Optimizeにルーティングされ、人間のトラフィックが影響を受けないことを確認できます。

1. **ボットトラフィックのテスト （最適化する必要があります）**

   エージェント型ユーザーエージェントを使用してAI ボットリクエストをシミュレートします。

   ```
   curl -svo /dev/null https://www.example.com/page.html \
     --header "user-agent: chatgpt-user"
   ```

   応答が成功すると、`x-edgeoptimize-request-id` ヘッダーが含まれ、リクエストがEdge Optimizeを通じてルーティングされたことが確認されます。

   ```
   < HTTP/2 200
   < x-edgeoptimize-request-id: 50fce12d-0519-4fc6-af78-d928785c1b85
   ```

2. **人間のトラフィックをテストします（影響を受けません）**

   通常のヒューマンブラウザーリクエストをシミュレートします。

   ```
   curl -svo /dev/null https://www.example.com/page.html \
     --header "user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36"
   ```

   応答に`x-edgeoptimize-request-id` ヘッダーを含めることはできません。 Edgeで最適化を有効にする前に、ページの内容と応答時間を同じにしておく必要があります。

3. **2つのシナリオを区別する方法**

   | ヘッダー | ボットトラフィック（最適化） | 人的トラフィック（影響なし） |
   |---|---|---|
   | `x-edgeoptimize-request-id` | 現在 – 一意のリクエスト IDを含む | 不在 |
   | `x-edgeoptimize-fo` | フェールオーバーが発生した場合にのみ存在します（値：`1`） | 不在 |

4. **LLM Optimizerのルーティング状況を確認**

   LLM Optimizer UIでルーティングを確認することもできます。 **顧客設定**&#x200B;を開き、**CDN設定** タブを選択します。 ルーティングがアクティブな場合、「**AI エージェントに最適化をデプロイ**」セクションに「**完了**」と表示されます。

   ![AI エージェントへの最適化のデプロイ – 完了](/help/assets/optimize-at-edge/cs-fastly-disable-button.png)

{{return-to-overview}}
