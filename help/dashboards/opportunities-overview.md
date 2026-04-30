---
title: 最適化の機会
description: オポチュニティダッシュボードを使用して、ブランドの可視性を高めることを目的に、サイトを改善できる方法を自動的に検出する方法について説明します。
feature: Opportunities
source-git-commit: b9e18081cd364b35a5375975cad949b7037bfaaf
workflow-type: tm+mt
source-wordcount: '1227'
ht-degree: 31%

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
| [LLMに適した概要を追加](/help/dashboards/opportunities/add-llm-friendly-summaries.md) | コンテンツ（オンサイト） | ページやセクションレベルで、簡潔な要約と構造化されたキーポイントが不足しているトラフィックの多いページを特定し、AI担当者がブランドの主張をスキャンして解釈するのを困難にします。 影響を受けるURLとサマリーが推奨される場所を表示します。 | AIが生成した要約と、既存のコンテンツに基づいたキーポイントを確認し、Edgeの最適化を使用してCDN エッジに展開することで、担当者がより明確でスキャン可能なコンテキストを受け取れるようにします。 |
| [関連するFAQを追加](/help/dashboards/opportunities/add-relevant-faqs.md) | コンテンツ（オンサイト） | プロンプトセットに沿った構造化されたQ&amp;A コンテンツが存在しない、トラフィックの多いページを特定します。これにより、AI エージェントがページに対するユーザーの質問を一致させることが困難になります。 影響を受けるURLとFAQが推奨される場所を表示します。 | 既存のページの素材に基づいて、AIが生成し、意図に沿ってFAQ コンテンツをレビューし、その後、EdgeのOptimize機能を使用してCDN エッジにデプロイすることで、担当者が明確なQ&amp;Aのコンテキストを受け取れるようにします。 |
| [ マルチメディア文字起こしサマリーの追加](/help/dashboards/opportunities/add-multimedia-transcript-summaries.md) | コンテンツ（オンサイト） | 機械で読み取れる文字起こしや要約がなくても、動画やその他のメディアに主要な情報が埋め込まれているページを特定できるため、AI エージェントがそのコンテンツを使用しにくくなります。 影響を受けるURLと推奨テキストを表示します。 | AIが生成した文字起こしサマリーをメディアとページに基づいて確認し、その後、Edgeの最適化を使用してCDN エッジにデプロイして、エージェントが機械読み取り可能なテキスト（関連するビデオの近くなど）を受け取れるようにします。 |
| [robots.txt](/help/dashboards/opportunities/traffic-blocked-by-robots.md)によってブロックされたトラフィック | テクニカル GEO | 一般に公開されているコンテンツからAI エージェントを選択的にブロックするルールについて、robots.txt ファイルを分析します。 影響を受けるURLとブロックされたエージェントをレポートします。 | robots.txt ファイルを更新して、サポートされているAI web クローラーへのアクセスを適切な場所で許可します。 |
| [ エージェント型トラフィックエラー](/help/dashboards/opportunities/agentic-traffic-errors.md) | テクニカル GEO | AI エージェントに返された404、403、および5xx エラー応答のCDN ログを監視します。 影響を受けるURLと失われたヒット数の合計をレポートします。 | 破損したリンクを修正し、権限を更新し、サーバーサイドの問題を解決して、主要なコンテンツが 200 応答を返すようにします。 |
| [複雑なコンテンツを簡略化](/help/dashboards/opportunities/simplify-complex-content.md) | コンテンツ（オンサイト） | 高密度または複雑なコピーが読みやすさのしきい値を下回っている、トラフィックの多いページを特定します。これにより、AI エージェントが重要な情報を解釈することが難しくなります。 影響を受けるURLと、簡略化されたテキストが推奨される場所を表示します。 | 既存のページコンテンツをベースにAIが生成した改善されたテキストを確認し、EdgeのOptimizeでCDN エッジにデプロイすることで、担当者がより明確でスキャンしやすい文章を受け取れるようにします。 |
| [コンテンツの可視性を復元](/help/dashboards/opportunities/recover-content-visibility.md) | テクニカル GEO | 重要なコンテンツが AI エージェントから非表示になっているページにフラグを付けます。 影響を受ける URL と回復可能な予想されるコンテンツを表示します。 | Edgeの最適化を使用して、CDN レイヤーのページを事前にレンダリングし、JavaScriptを実行せずにAI エージェントがより多くのコンテンツを利用できるようにします。 |
| [目次を追加](/help/dashboards/opportunities/add-table-of-contents.md) | テクニカル GEO | 明確な構造化やナビゲーションの見出しに欠けるページを検出し、AI エージェントがコンテンツを解析してユーザークエリにマッピングするのを困難にします。 影響を受けるURLと、構造化された目次を推奨する場所を表示します。 | 推奨される構造化目次と、ページの主なセクションを反映するアンカー付きの見出しを確認し、Edgeで最適化を使用してCDN エッジにデプロイすると、目次がHTMLに挿入され、ページ構造が改善され、モデルが関連するセクションをより簡単に抽出、マッピング、引用できるようになります。 |
| [Wikipedia Analysis](/help/dashboards/opportunities/wikipedia-analysis.md) | オフサイト | 参照、セクション、コンテンツの長さ、画像、インフォボックスの完全性など、業界の競合他社に対して会社のWikipedia ページを分析します。 自社のページが業界のベンチマークを下回っている特定のギャップを特定。 | 参照の追加、インフォボックスの充実、セクションの拡大、記事の質の向上など、Wikipediaの存在感を向上させるためのAI生成の戦略的レコメンデーションを確認できます。 |
| [YouTube センチメント分析（Beta） ](/help/dashboards/opportunities/youtube-sentiment-analysis.md) | オフサイト、ソーシャル、コミュニティ | ブランド言及、センチメント、シェアオブボイス、繰り返しのトピックに関するブランドプレゼンスプロンプトセットに対して引用されたYouTube ビデオを分析します。 YouTube ビデオがプロンプトセットの引用として検出された場合にのみ表示されます。 | 提案されたアクションと、その導入を担当するチームも含め、YouTubeのコンテンツ全体でブランド認知を向上させるために、優先順位が設定されたレコメンデーションを確認します。 |
| [Reddit センチメント分析（Beta） ](/help/dashboards/opportunities/reddit-sentiment-analysis.md) | オフサイト、ソーシャル、コミュニティ | ブランド言及、センチメント、シェアオブボイス、繰り返しトピックのブランドプレゼンスプロンプトセットに対して引用されたReddit スレッドを分析します。 Reddit スレッドがプロンプトセットの引用として検出された場合にのみ表示されます。 | 優先順位の高い推奨事項を確認し、提案されたアクションとその実装を担当するチームなど、Redditのコンテンツ全体でブランドの認識を向上させます。 |
| [引用センチメント分析（Beta） ](/help/dashboards/opportunities/cited-sentiment-analysis.md) | オフサイト、ソーシャル、コミュニティ | ブランド言及、センチメント、シェアオブボイス、繰り返しトピックに関するブランドプレゼンスプロンプトのセットで検出された上位のURLを分析します。 | AI システムが最も頻繁に引用するページで、ブランドに関するプロンプトに応答する際に、優先順位が付けられた推奨事項を確認し、ブランドの認識を向上させます。 |
| [商品カタログの拡充（Beta） ](/help/dashboards/opportunities/enrich-product-catalog.md) | コンテンツ（オンサイト）, Adobe Commerce | LLMが解釈できない、汎用的な、技術的に密度の高い、または曖昧な名前または説明を持つCommerce カタログ製品を識別します。 評価済みのPDP、Agentic traffic context、AI生成のナラティブ強化を表示します。 | 提案された製品名と説明を確認および編集し、最適化をデプロイして、更新をAdobe Commerce カタログに直接公開します（修正済みの提案からのロールバックを使用）。 |
| [製品詳細ページの拡充](/help/dashboards/opportunities/enrich-product-detail-pages.md) | テクニカル地域，Adobe Commerce | Adobe Commerce ストアフロントの場合、完全なカタログデータを、AI担当者が各製品詳細ページでアクセスできるデータと比較します。エージェントのトラフィックによって優先順位付けされた、エージェントに表示されるHTMLからバリエーション、仕様、属性、関連カタログフィールドが欠落しているPDPを表示します。 | エージェントのビューから復元可能なカタログ情報をハイライト表示し、LLM主導の製品発見が重要な理由を示します。Edgeで最適化をデプロイして、完全にレンダリングされたAI フレンドリーなHTML スナップショットをCDN エッジのエージェンティックトラフィックに提供することで、エージェントはCMSやカタログの変更なしに、カタログから豊富な製品コンテキストを受け取ることができます。 |

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
