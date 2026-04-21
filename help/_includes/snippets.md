---
source-git-commit: b13f91d144d4899198891c4dcd841de8cfbb2355
workflow-type: tm+mt
source-wordcount: '157'
ht-degree: 0%

---
# スニペット

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
