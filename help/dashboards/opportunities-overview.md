---
title: 最適化の機会
description: オポチュニティダッシュボードを使用して、ブランドの可視性を高めることを目的に、サイトを改善できる方法を自動的に検出する方法について説明します。
feature: Opportunities
autotag-review: '2026-05-15T17:53:48.623Z'
TQID: 'https://experienceleague.adobe.com/FAbQhzuyT-kIitIaoVQ47dam-TpN-deU5Vbo1nmK5CA'
product_v2:
  - id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2:
  - id: a0b5a505-2fd7-4c3d-b61c-b557fb6f0558
  - id: c0713b97-4af8-4c41-b742-5afcc6ced468
subfeature_v2:
  - id: e1b649f0-0a61-46e4-9082-64d5cb2576c6
topic_v2:
  - id: cdd65e7e-8839-44a2-bc21-0e03623b5dd1
  - id: e1e0219c-f879-479f-8427-888ed2a6e9c2
source-git-commit: 564171851fdccee43afd233da143d66182464889
workflow-type: tm+mt
source-wordcount: 1227
ht-degree: 56%

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
| [&#x200B; マルチメディア文字起こしサマリーの追加](/help/dashboards/opportunities/add-multimedia-transcript-summaries.md) | コンテンツ（オンサイト） | 機械で読み取れる文字起こしや要約がなくても、動画やその他のメディアに主要な情報が埋め込まれているページを特定できるため、AI エージェントがそのコンテンツを使用しにくくなります。 影響を受けるURLと推奨テキストを表示します。 | AIが生成した文字起こしサマリーをメディアとページに基づいて確認し、その後、Edgeの最適化を使用してCDN エッジにデプロイして、エージェントが機械読み取り可能なテキスト（関連するビデオの近くなど）を受け取れるようにします。 |
| [robots.txt によってブロックされたトラフィック](/help/dashboards/opportunities/traffic-blocked-by-robots.md) | テクニカル GEO | robots.txt ファイルを分析し、通常は一般公開されているコンテンツに対して、AI エージェントによるアクセスを選択的にブロックするルールを検出します。 影響を受ける URL とブロックされたエージェントをレポートします。 | 必要に応じて、robots.txt ファイルを更新して、サポートされている AI クローラーのアクセスを許可します。 |
| [エージェントトラフィックエラー](/help/dashboards/opportunities/agentic-traffic-errors.md) | テクニカル GEO | AI エージェントに返される 404、403、5xx エラー応答について、CDN ログを監視します。 影響を受ける URL と失われた合計ヒット数をレポートします。 | 破損したリンクを修正し、権限を更新し、サーバーサイドの問題を解決して、主要なコンテンツが 200 応答を返すようにします。 |
| [複雑なコンテンツを簡略化](/help/dashboards/opportunities/simplify-complex-content.md) | コンテンツ（オンサイト） | 高密度または複雑なコピーが読みやすさのしきい値を下回っている、トラフィックの多いページを特定します。これにより、AI エージェントが重要な情報を解釈することが難しくなります。 影響を受けるURLと、簡略化されたテキストが推奨される場所を表示します。 | 既存のページコンテンツをベースにAIが生成した改善されたテキストを確認し、EdgeのOptimizeでCDN エッジにデプロイすることで、担当者がより明確でスキャンしやすい文章を受け取れるようにします。 |
| [コンテンツの可視性を回復](/help/dashboards/opportunities/recover-content-visibility.md) | テクニカル GEO | 重要なコンテンツが AI エージェントから非表示になっているページにフラグを付けます。 影響を受ける URL と回復可能な予想されるコンテンツを表示します。 | Edge での最適化を使用して CDN レイヤーでページを事前レンダリングすることで、JavaScript を実行せずに AI エージェントがより多くのコンテンツを利用できるようにします。 |
| [目次を追加](/help/dashboards/opportunities/add-table-of-contents.md) | テクニカル GEO | 明確な構造化やナビゲーションの見出しに欠けるページを検出し、AI エージェントがコンテンツを解析してユーザークエリにマッピングするのを困難にします。 影響を受けるURLと、構造化された目次を推奨する場所を表示します。 | 推奨される構造化目次と、ページの主なセクションを反映するアンカー付きの見出しを確認し、Edgeで最適化を使用してCDN エッジにデプロイすると、目次がHTMLに挿入され、ページ構造が改善され、モデルが関連するセクションをより簡単に抽出、マッピング、引用できるようになります。 |
| [Wikipedia 分析](/help/dashboards/opportunities/wikipedia-analysis.md) | オフサイト | 参照、セクション、コンテンツの長さ、画像、情報ボックスの完成度をまたいで会社の Wikipedia ページを業界の競合他社と比較して分析します。 ページが業界ベンチマークを下回っている具体的なギャップを特定します。 | 参照の追加、情報ボックスの強化、セクションの拡張、記事の品質の向上など、Wikipedia プレゼンスを高めるための AI 生成の戦略的なレコメンデーションを確認します。 |
| [YouTube のセンチメント分析（Beta）](/help/dashboards/opportunities/youtube-sentiment-analysis.md) | オフサイト、ソーシャル、コミュニティ | ブランド言及、センチメント、シェアオブボイス、繰り返しトピックに対するブランドプレゼンスのプロンプトセットで引用された YouTube ビデオを分析します。 プロンプトセットの引用として YouTube ビデオが検出された場合にのみ表示されます。 | YouTube コンテンツをまたいでブランドイメージを向上させるための優先順位が付けられたレコメンデーションを確認します。レコメンデーションには、候補アクションと、これらの実装を担当するチームが含まれます。 |
| [Reddit のセンチメント分析（Beta）](/help/dashboards/opportunities/reddit-sentiment-analysis.md) | オフサイト、ソーシャル、コミュニティ | ブランド言及、センチメント、シェアオブボイス、繰り返しトピックに対するブランドプレゼンスのプロンプトセットで引用された Reddit のスレッドを分析します。 プロンプトセットの引用として Reddit のスレッドが検出された場合にのみ表示されます。 | Reddit コンテンツをまたいでブランドイメージを向上させるための優先順位が付けられたレコメンデーションを確認します。レコメンデーションには、候補アクションと、これらの実装を担当するチームが含まれます。 |
| [引用されたセンチメント分析（Beta）](/help/dashboards/opportunities/cited-sentiment-analysis.md) | オフサイト、ソーシャル、コミュニティ | ブランド言及、センチメント、シェアオブボイス、繰り返しトピックに対するブランドプレゼンスのプロンプトセットで検出された上位の引用 URL を分析します。 | AI システムがブランドに関するプロンプトに応答する際に最も多く引用するページをまたいでブランドイメージを向上させるための優先順位が付けられたレコメンデーションを確認します。 |
| [商品カタログの拡充（Beta） &#x200B;](/help/dashboards/opportunities/enrich-product-catalog.md) | コンテンツ（オンサイト）, Adobe Commerce | LLMが解釈できない、汎用的な、技術的に密度の高い、または曖昧な名前または説明を持つCommerce カタログ製品を識別します。 評価済みのPDP、Agentic traffic context、AI生成のナラティブ強化を表示します。 | 提案された製品名と説明を確認および編集し、最適化をデプロイして、更新をAdobe Commerce カタログに直接公開します（修正済みの提案からのロールバックを使用）。 |
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
