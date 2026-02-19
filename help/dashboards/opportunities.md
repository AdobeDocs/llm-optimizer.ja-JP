---
title: 最適化の機会
description: オポチュニティダッシュボードを使用して、ブランドの可視性を高めることを目的に、サイトを改善できる方法を自動的に検出する方法について説明します。
feature: Opportunities
source-git-commit: 82830e66d43ddd9741617cdf6daab63cd259554b
workflow-type: tm+mt
source-wordcount: '575'
ht-degree: 100%

---


# 最適化の機会

最適化の機会とは、AI 検索でのブランドの可視性を高めることを目的に、サイトと外部プレゼンスを改善できる箇所を示す、自動検出されたインサイトです。

これらの最適化には、ページ上の修正（構造化コンテンツ、正規表現、要約の追加）、技術的な調整（AI クローラーのブロック解除やエラーの解決）、サードパーティの信頼できるサイトのコンテンツへの影響が含まれます。 これらの最適化の機会に対処することで、ブランドが正確に示され、生成される応答で引用される可能性が高まります。

![最適化の機会](/help/dashboards/assets/oport.png)

## オポチュニティダッシュボード

ダッシュボードに表示される最適化の機会は、プレーヤーのギャップ、トレンド分析のトピック、パフォーマンスデータに基づいて優先順位が付けられ、リストとして表示されます。 検索フィールドを使用して、特定のオポチュニティを検索できます。 また、機会はタグ別にグループ化され、タグを直接クリックすると、そのタグの下にグループ化されているすべての機会が表示されます。

「**詳細**」をクリックすると、別のウィンドウが開き、その他の情報と追加のガイダンスが表示されます。

## サポートされる機会

現在サポートされている機会の表を以下に示します。

| オポチュニティ | タイプ | 特定された問題 | 修正候補 |
|---------|----------|----------|----------|
| 長い段落を要約 | コンテンツ（オンサイト） | 推奨される長さのしきい値を超える段落を検出します。 影響を受ける URL とサイズが大きすぎるテキストスニペットを表示します。 | 要約を作成するか、長いテキストを短いスキャン可能なセクションに分割します。 |
| 構造化コンテンツ（FAQ）を推奨 | コンテンツ（オンサイト） | 一致する FAQ エントリがない、人気の高いプロンプトを検出します。 関連するプロンプト、カテゴリ、影響を受ける URL を表示します。 | よくあるクエリに一致する簡潔な回答を含む FAQ スキーマブロックを追加します。 |
| 欠落している hreflang を検出 | コンテンツ（オンサイト） | hreflang 属性が欠落しているページにフラグを付けます。 影響を受ける URL と、言語／地域別に予想されるカバレッジを示します。 | hreflang タグを実装して、正しいローカライズバージョンを示します。 |
| 欠落している正規表現を検出 | コンテンツ（オンサイト） | 正規タグのないページや競合するタグのあるページをスキャンします。 影響を受ける URL と重複を一覧表示します。 | 各ページの優先バージョンを指す正規タグを追加します。 バリアントをまたいで一貫性のある使用を確保します。 |
| ブロックされたエージェントトラフィックを検出 | テクニカル GEO | 既知の AI エージェント（例：GPTBot、PerplexityBot）からのブロックされたリクエストの CDN ログを分析します。 影響を受ける URL とエージェントをレポートします。 | 必要に応じて、robots.txt またはサーバー設定を更新して、サポートされている AI クローラーのアクセスを許可します。 |
| 404s／403s／5xx の問題を検出 | テクニカル GEO | CDN ログでエラー応答を監視します。 頻度、影響を受ける URL、失われた推定ヒット数をレポートします。 | 破損したリンクを修正し、権限を更新し、サーバーサイドの問題を解決して、主要なコンテンツが 200 応答を返すようにします。 |
| コンテンツの可視性を回復（早期アクセス） | テクニカル GEO | 重要なコンテンツが AI エージェントから非表示になっているページにフラグを付けます。 影響を受ける URL と回復可能な予想されるコンテンツを表示します。 | ページを事前レンダリングすることで、JavaScript を実行せずに AI エージェントがより多くのコンテンツを利用できるようにします。 |

## 自動最適化 {#auto-optimization}

自動最適化により、推奨される最適化のワンクリックデプロイメントを実行できるので、手動の労力と価値実現までの時間が削減されます。 最適化は、コンテンツソースまたは CDN Edge のいずれかで適用できます。 Edge ベースの自動最適化は現在、一部の機会に早期アクセスで使用できます。 詳しくは、[Edge での最適化](/help/dashboards/optimize-at-edge/overview.md)ページを参照してください。

<!--### Recover Content Visibility Opportunity {#recover-contet}

As stated above, the content visibility opportunity, flags pages where key content is lost for AI agents due to client-side rendering. For each identified page, it shows you exactly which content is missing from the AI agent view, helping you pinpoint visibility gaps. It's also supported by an edge-based pre-rendering capability that can serve more HTML content to agentic traffic without requiring Content Management System (CMS) changes. This functionality is currently in Early Access and requires setup from the LLM Optimizer team. Please contact `llmo-at-edge@adobe.com` to activate the content visibility opportunity.-->

### その他のツール

[LLM 可視性チェッカー](https://chromewebstore.google.com/detail/is-your-webpage-citable/jbjngahjjdgonbeinjlepfamjdmdcbcc)は、LLM が web ページのコンテンツのどの程度にアクセスでき、またどの部分が非表示になっているかを正確に確認できる Chrome 拡張機能です。 無料のスタンドアロン診断ツールとして設計され、製品ライセンスや設定は必要ありません。 ユーザーはシングルクリックで、任意のサイトの機械による読みやすさを評価し、AI エージェントに表示される内容と人間のユーザーに表示される内容を並べて比較表示できます。 また、LLM Optimizer を使用して回復できるコンテンツの量を推定します。
