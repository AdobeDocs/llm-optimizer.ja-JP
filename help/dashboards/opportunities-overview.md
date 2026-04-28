---
title: 最適化の機会
description: オポチュニティダッシュボードを使用して、ブランドの可視性を高めることを目的に、サイトを改善できる方法を自動的に検出する方法について説明します。
feature: Opportunities
source-git-commit: 34e90bc95aa1d2ffabe8fd06c2c548491dd5c5b7
workflow-type: tm+mt
source-wordcount: '780'
ht-degree: 58%

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
| 構造化コンテンツのレコメンデーション | コンテンツ（オンサイト） | 一致する FAQ エントリがない、人気の高いプロンプトを検出します。 関連するプロンプト、カテゴリ、影響を受ける URL を表示します。 | よくあるクエリに一致する簡潔な回答を含む FAQ スキーマブロックを追加します。 |
| [robots.txt](/help/dashboards/opportunities/traffic-blocked-by-robots.md)によってブロックされたトラフィック | テクニカル GEO | 一般に公開されているコンテンツからAI エージェントを選択的にブロックするルールについて、robots.txt ファイルを分析します。 影響を受けるURLとブロックされたエージェントをレポートします。 | robots.txt ファイルを更新して、サポートされているAI web クローラーへのアクセスを適切な場所で許可します。 |
| [&#x200B; エージェント型トラフィックエラー](/help/dashboards/opportunities/agentic-traffic-errors.md) | テクニカル GEO | AI エージェントに返された404、403、および5xx エラー応答のCDN ログを監視します。 影響を受けるURLと失われたヒット数の合計をレポートします。 | 破損したリンクを修正し、権限を更新し、サーバーサイドの問題を解決して、主要なコンテンツが 200 応答を返すようにします。 |
| 複雑なコンテンツの簡素化 | コンテンツ（オンサイト） | AIの理解度を下げることができる、読みやすいしきい値を超えた長くて複雑な段落を特定します。 | ページを事前レンダリングすることで、JavaScript を実行せずに AI エージェントがより多くのコンテンツを利用できるようにします。 |
| [コンテンツの可視性を復元](/help/dashboards/opportunities/recover-content-visibility.md) | テクニカル GEO | 重要なコンテンツが AI エージェントから非表示になっているページにフラグを付けます。 影響を受ける URL と回復可能な予想されるコンテンツを表示します。 | Edgeの最適化を使用して、CDN レイヤーのページを事前にレンダリングし、JavaScriptを実行せずにAI エージェントがより多くのコンテンツを利用できるようにします。 |
| [Wikipedia Analysis](/help/dashboards/opportunities/wikipedia-analysis.md) | オフサイト | 参照、セクション、コンテンツの長さ、画像、インフォボックスの完全性など、業界の競合他社に対して会社のWikipedia ページを分析します。 自社のページが業界のベンチマークを下回っている特定のギャップを特定。 | 参照の追加、インフォボックスの充実、セクションの拡大、記事の質の向上など、Wikipediaの存在感を向上させるためのAI生成の戦略的レコメンデーションを確認できます。 |
| [YouTube センチメント分析（Beta） &#x200B;](/help/dashboards/opportunities/youtube-sentiment-analysis.md) | オフサイト、ソーシャル、コミュニティ | ブランド言及、センチメント、シェアオブボイス、繰り返しのトピックに関するブランドプレゼンスプロンプトセットに対して引用されたYouTube ビデオを分析します。 YouTube ビデオがプロンプトセットの引用として検出された場合にのみ表示されます。 | 提案されたアクションと、その導入を担当するチームも含め、YouTubeのコンテンツ全体でブランド認知を向上させるために、優先順位が設定されたレコメンデーションを確認します。 |
| [Reddit センチメント分析（Beta） &#x200B;](/help/dashboards/opportunities/reddit-sentiment-analysis.md) | オフサイト、ソーシャル、コミュニティ | ブランド言及、センチメント、シェアオブボイス、繰り返しトピックのブランドプレゼンスプロンプトセットに対して引用されたReddit スレッドを分析します。 Reddit スレッドがプロンプトセットの引用として検出された場合にのみ表示されます。 | 優先順位の高い推奨事項を確認し、提案されたアクションとその実装を担当するチームなど、Redditのコンテンツ全体でブランドの認識を向上させます。 |
| [引用センチメント分析（Beta） &#x200B;](/help/dashboards/opportunities/cited-sentiment-analysis.md) | オフサイト、ソーシャル、コミュニティ | ブランド言及、センチメント、シェアオブボイス、繰り返しトピックに関するブランドプレゼンスプロンプトのセットで検出された上位のURLを分析します。 | AI システムが最も頻繁に引用するページで、ブランドに関するプロンプトに応答する際に、優先順位が付けられた推奨事項を確認し、ブランドの認識を向上させます。 |

## 自動最適化 {#auto-optimization}

自動最適化により、推奨される最適化のワンクリックデプロイメントを実行できるので、手動の労力と価値実現までの時間が削減されます。 最適化は、コンテンツソースまたは CDN Edge のいずれかで適用できます。 Edge ベースの自動最適化は現在、一部の機会に早期アクセスで使用できます。 詳しくは、[Edge での最適化](/help/dashboards/optimize-at-edge/overview.md)ページを参照してください。

<!--
### Recover Content Visibility Opportunity {#recover-contet}

As stated above, the content visibility opportunity, flags pages where key content is lost for AI agents due to client-side rendering. For each identified page, it shows you exactly which content is missing from the AI agent view, helping you pinpoint visibility gaps. It's also supported by an edge-based pre-rendering capability that can serve more HTML content to agentic traffic without requiring Content Management System (CMS) changes. This functionality is currently in Early Access and requires setup from the LLM Optimizer team. Please contact `llmo-at-edge@adobe.com` to activate the content visibility opportunity.
-->

### その他のツール

[LLM 可視性チェッカー](https://chromewebstore.google.com/detail/is-your-webpage-citable/jbjngahjjdgonbeinjlepfamjdmdcbcc)は、LLM が web ページのコンテンツのどの程度にアクセスでき、またどの部分が非表示になっているかを正確に確認できる Chrome 拡張機能です。 無料のスタンドアロン診断ツールとして設計され、製品ライセンスや設定は必要ありません。 ユーザーはシングルクリックで、任意のサイトの機械による読みやすさを評価し、AI エージェントに表示される内容と人間のユーザーに表示される内容を並べて比較表示できます。 また、LLM Optimizer を使用して回復できるコンテンツの量を推定します。

<!--
| Detect Missing Hreflang | Content (Onsite)| Flags pages missing hreflang attributes. Provides affected URLs and expected coverage by language/region.| Implement hreflang tags to indicate correct localized versions. |
| Detect Missing Canonicals | Content (Onsite) | Scans for pages without canonical tags or with conflicting tags. Lists affected URLs and duplicates. | Add canonical tags pointing to the preferred version of each page. Ensure consistent usage across variants. |
-->
