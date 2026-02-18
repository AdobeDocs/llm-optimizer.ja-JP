---
source-git-commit: beae935e7a34f5bccbe21578fa9a928912958710
workflow-type: tm+mt
source-wordcount: '467'
ht-degree: 2%

---
# スニペット

## 設定の確認 – Adobeの管理による CDN {#verify-setup-adobe-aem-cs-cdn}

**設定の確認**

設定が完了したら、ボットトラフィックがEdge Optimize にルーティングされていることと、人間のトラフィックが影響を受けないことを確認します。

**1. ボットトラフィックのテスト （最適化する必要があります）**

エージェント user-agent を使用して AI ボットリクエストをシミュレートします。

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: chatgpt-user"
```

リクエストが成功した応答には `x-edgeoptimize-request-id` ヘッダーが含まれ、リクエストがEdge Optimize を通じてルーティングされたことを確認します。

```
< HTTP/2 200
< x-edgeoptimize-request-id: 50fce12d-0519-4fc6-af78-d928785c1b85
```

**2. 人間によるトラフィックのテスト（影響を受けない）**

通常の人間によるブラウザーリクエストをシミュレートする：

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36"
```

応答には、`x-edgeoptimize-request-id` ヘッダーを含める **しない** でください。 Edgeで最適化を有効にする前と同じページコンテンツおよび応答時間である必要があります。

**3. 2 つのシナリオの違い**

| ヘッダー | ボットトラフィック（最適化） | ヒューマントラフィック（影響を受けない） |
|---|---|---|
| `x-edgeoptimize-request-id` | 現在 – 一意のリクエスト ID が含まれます | 不在 |
| `x-edgeoptimize-fo` | フェールオーバーが発生した場合にのみ表示されます（値：`1`） | 不在 |

トラフィックルーティングのステータスは、LLM Optimizer UI でも確認できます。 **顧客設定** に移動し、「**CDN 設定**」タブを選択します。

![ ルーティングを有効にした AI トラフィックルーティングステータス ](/help/assets/optimize-at-edge/adobe-CDN-traffic-routed-tick.png)

## 設定の確認 – BYOCDN {#verify-setup-byocdn}

**設定の確認**

設定が完了したら、ボットトラフィックがEdge Optimize にルーティングされていることと、人間のトラフィックが影響を受けないことを確認します。

**1. ボットトラフィックのテスト （最適化する必要があります）**

エージェント user-agent を使用して AI ボットリクエストをシミュレートします。

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: chatgpt-user"
```

リクエストが成功した応答には `x-edgeoptimize-request-id` ヘッダーが含まれ、リクエストがEdge Optimize を通じてルーティングされたことを確認します。

```
< HTTP/2 200
< x-edgeoptimize-request-id: 50fce12d-0519-4fc6-af78-d928785c1b85
```

**2. 人間によるトラフィックのテスト（影響を受けない）**

通常の人間によるブラウザーリクエストをシミュレートする：

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36"
```

応答には、`x-edgeoptimize-request-id` ヘッダーを含める **しない** でください。 Edgeで最適化を有効にする前と同じページコンテンツおよび応答時間である必要があります。

**3. 2 つのシナリオの違い**

| ヘッダー | ボットトラフィック（最適化） | ヒューマントラフィック（影響を受けない） |
|---|---|---|
| `x-edgeoptimize-request-id` | 現在 – 一意のリクエスト ID が含まれます | 不在 |
| `x-edgeoptimize-fo` | フェールオーバーが発生した場合にのみ表示されます（値：`1`） | 不在 |

トラフィックルーティングのステータスは、LLM Optimizer UI でも確認できます。 **顧客設定** に移動し、「**CDN 設定**」タブを選択します。

![ ルーティングを有効にした AI トラフィックルーティングステータス ](/help/assets/optimize-at-edge/byocdn-CDN-traffic-routed-tick.png)

## API キー取得手順 {#retrieve-byocdn-api-key}

**API キーを取得する手順：**

1. **顧客設定** に移動し、「**CDN 設定**」タブを選択します。

   ![ 顧客設定に移動 ](/help/assets/optimize-at-edge/prereq-customer-config-nav.png)

2. 「**最適化をデプロイするための AI トラフィックルーティング**」で、「**最適化を AI エージェントにデプロイ**」チェックボックスをオンにします。

   ![AI エージェントへのティックのデプロイの最適化 ](/help/assets/optimize-at-edge/prereq-deploy-checkbox.png)

3. API キーをコピーして、以下のルーティング設定手順に進みます。

   ![API キーをコピーします ](/help/assets/optimize-at-edge/prereq-copy-api-key.png)

   >[!NOTE]
   >この段階では、ステータスに、設定がまだ完了していないことを示す赤い十字が表示される場合があります。 これは想定されています。以下のルーティング設定を完了し、AI ボットトラフィックのフローが開始されると、ステータスが緑色のチェックマークに更新され、ルーティングが正常に有効であることが確認されます。

さらに、上記の手順に関するヘルプが必要な場合は、Adobe アカウントチームまたは `llmo-at-edge@adobe.com` に問い合わせてください。

## 概要に戻る {#return-to-overview}

利用可能なオポチュニティ、自動最適化ワークフロー、FAQ など、Edgeでの最適化について詳しくは、[Edgeでの最適化の概要 ](/help/dashboards/optimize-at-edge.md) を参照してください。
