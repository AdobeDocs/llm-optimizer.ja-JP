---
title: 最適化の機会
description: オポチュニティダッシュボードを使用して、ブランドの可視性を高めることを目的に、サイトを改善できる方法を自動的に検出する方法について説明します。
feature: Opportunities
autotag-review: '2026-07-15T18:08:26.657Z'
TQID: 'https://experienceleague.adobe.com/nEVOXJiQZIqfs2Q-tpA3DeiBNwpZAjhMSyIVuNlACdM'
product_v2:
  - id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2:
  - id: e1b649f0-0a61-46e4-9082-64d5cb2576c6
  - id: ef4e63f5-cb4d-462d-bf9a-1f617edf2a3a
subfeature_v2:
  - id: bbfc1b77-44c5-4fe8-b65f-ec160fe0d021
  - id: a6256a78-8814-462c-9627-86699b39cee1
  - id: e0ec491f-fe51-42b6-801c-1c0dfcc0e64f
  - id: fe92ae96-fc87-4fea-96a0-adc06310d4f4
role_v2:
  - id: b69b2659-1057-424e-8fc5-ed9e016dc554
topic_v2:
  - id: cdd65e7e-8839-44a2-bc21-0e03623b5dd1
  - id: e1e0219c-f879-479f-8427-888ed2a6e9c2
source-git-commit: 2705cf26faea9c09817bbdcec4b4c531552df7ba
workflow-type: ht
source-wordcount: 1225
ht-degree: 100%

---


# 最適化の機会

最適化の機会とは、AI 検索でのブランドの可視性を高めることを目的に、サイトと外部プレゼンスを改善できる箇所を示す、自動検出されたインサイトです。

これらの最適化には、ページ上の修正（構造化コンテンツ、正規表現、要約の追加）、技術的な調整（AI クローラーのブロック解除やエラーの解決）、サードパーティの信頼できるサイトのコンテンツへの影響が含まれます。 これらの最適化の機会に対処することで、ブランドが正確に示され、生成される応答で引用される可能性が高まります。

<!--![Optimization opportunities](/help/dashboards/assets/oport.png)-->

## オポチュニティダッシュボード

ダッシュボードに表示される最適化の機会は、プレーヤーのギャップ、トレンド分析のトピック、パフォーマンスデータに基づいて優先順位が付けられ、リストとして表示されます。 検索フィールドを使用して、特定のオポチュニティを検索できます。 また、機会はタグ別にグループ化され、タグを直接クリックすると、そのタグの下にグループ化されているすべての機会が表示されます。

「**詳細**」をクリックすると、別のウィンドウが開き、その他の情報と追加のガイダンスが表示されます。

## サポートされる機会

現在サポートされている機会の表を以下に示します。

| オポチュニティ | タイプ | 特定された問題 | 修正候補 |
|---------|----------|----------|----------|
| [LLM に対応した概要を追加](/help/dashboards/opportunities/add-llm-friendly-summaries.md) | コンテンツ（オンサイト） | ページレベルやセクションレベルで簡潔な概要や構造化された重要なポイントがなく、ブランドメッセージを AI エージェントがスキャンして解釈するのが難しくなるトラフィックが多いページを特定します。影響を受ける URL と、概要の追加が推奨される箇所を表示します。 | 既存のコンテンツに基づいて AI が生成した要約と要点を確認し、Edge での最適化を使用して CDN エッジにデプロイします。これにより、AI エージェントは、より明確で把握しやすいコンテキストを受け取れるようになります。 |
| [関連する FAQ を追加](/help/dashboards/opportunities/add-relevant-faqs.md) | コンテンツ（オンサイト） | プロンプトセットに適合する構造化された Q&amp;A コンテンツがなく、AI エージェントがユーザーの質問とページを一致させるのが難しくなるトラフィックが多いページを特定します。影響を受ける URL と、FAQ の追加が推奨される箇所を表示します。 | 既存のページコンテンツに基づいて AI が生成した、ユーザーの意図に沿った FAQ コンテンツを確認し、Edge での最適化を使用して CDN エッジにデプロイします。これにより、AI エージェントは、より明確な Q&amp;A コンテキストを受け取れるようになります。 |
| [マルチメディアの文字起こし要約を追加](/help/dashboards/opportunities/add-multimedia-transcript-summaries.md) | コンテンツ（オンサイト） | 機械で読み取れるトランスクリプトや要約がなくても、ビデオやその他のメディアに重要な情報が埋め込まれ、AI エージェントがそのコンテンツを使用するのが難しくなるページを特定します。影響を受ける URL と推奨テキストを表示します。 | メディアとページの内容に基づいて AI が生成した文字起こし要約を確認し、Edge での最適化を使用して CDN エッジにデプロイします。これにより、AI エージェントは、機械が読み取り可能なテキスト（関連する動画の近くに配置されるものなど）を受け取れるようになります。 |
| [robots.txt によってブロックされたトラフィック](/help/dashboards/opportunities/traffic-blocked-by-robots.md) | テクニカル GEO | robots.txt ファイルを分析し、通常は一般公開されているコンテンツに対して、AI エージェントによるアクセスを選択的にブロックするルールを検出します。 影響を受ける URL とブロックされたエージェントをレポートします。 | 必要に応じて、robots.txt ファイルを更新して、サポートされている AI クローラーのアクセスを許可します。 |
| [エージェントトラフィックエラー](/help/dashboards/opportunities/agentic-traffic-errors.md) | テクニカル GEO | AI エージェントに返される 404、403、5xx エラー応答について、CDN ログを監視します。 影響を受ける URL と失われた合計ヒット数をレポートします。 | 破損したリンクを修正し、権限を更新し、サーバーサイドの問題を解決して、主要なコンテンツが 200 応答を返すようにします。 |
| [複雑なコンテンツを簡潔化](/help/dashboards/opportunities/simplify-complex-content.md) | コンテンツ（オンサイト） | 可読性の基準を下回る、密度が高いまたは複雑な文章が含まれている高トラフィックページを特定します。このようなページでは、AI エージェントが重要な情報を解釈しにくくなります。影響を受ける URL と、簡潔な文章への変更が推奨される箇所を表示します。 | 既存のページコンテンツに基づいて AI が生成した改善済みテキストを確認し、Edge での最適化を使用して CDN エッジにデプロイします。これにより、AI エージェントは、より明確で読み取りやすい文章を受け取れるようになります。 |
| [コンテンツの可視性を回復](/help/dashboards/opportunities/recover-content-visibility.md) | テクニカル GEO | 重要なコンテンツが AI エージェントから非表示になっているページにフラグを付けます。 影響を受ける URL と回復可能な予想されるコンテンツを表示します。 | Edge での最適化を使用して CDN レイヤーでページを事前レンダリングすることで、JavaScript を実行せずに AI エージェントがより多くのコンテンツを利用できるようにします。 |
| [目次を追加](/help/dashboards/opportunities/add-table-of-contents.md) | テクニカル GEO | 明確な構造組織やナビゲーションの見出しがなく、AI エージェントがコンテンツを解析し、ユーザーのクエリにマッピングするのが難しくなるページを検出します。影響を受ける URL と、構造化された目次の追加が推奨される箇所を表示します。 | ページの主なセクションを反映したアンカーリンク付きの構造化された目次を確認し、Edge での最適化を使用して CDN エッジにデプロイします。これにより、目次が HTML に挿入され、ページ構造が改善されるため、モデルは関連するセクションをより簡単に抽出、対応付け、引用できるようになります。 |
| [Wikipedia 分析](/help/dashboards/opportunities/wikipedia-analysis.md) | オフサイト | 参照、セクション、コンテンツの長さ、画像、情報ボックスの完成度をまたいで会社の Wikipedia ページを業界の競合他社と比較して分析します。 ページが業界ベンチマークを下回っている具体的なギャップを特定します。 | 参照の追加、情報ボックスの強化、セクションの拡張、記事の品質の向上など、Wikipedia プレゼンスを高めるための AI 生成の戦略的なレコメンデーションを確認します。 |
| [YouTube のセンチメント分析（Beta）](/help/dashboards/opportunities/youtube-sentiment-analysis.md) | オフサイト、ソーシャル、コミュニティ | ブランド言及、センチメント、シェアオブボイス、繰り返しトピックに対するブランドプレゼンスのプロンプトセットで引用された YouTube ビデオを分析します。 プロンプトセットの引用として YouTube ビデオが検出された場合にのみ表示されます。 | YouTube コンテンツをまたいでブランドイメージを向上させるための優先順位が付けられたレコメンデーションを確認します。レコメンデーションには、候補アクションと、これらの実装を担当するチームが含まれます。 |
| [Reddit のセンチメント分析（Beta）](/help/dashboards/opportunities/reddit-sentiment-analysis.md) | オフサイト、ソーシャル、コミュニティ | ブランド言及、センチメント、シェアオブボイス、繰り返しトピックに対するブランドプレゼンスのプロンプトセットで引用された Reddit のスレッドを分析します。 プロンプトセットの引用として Reddit のスレッドが検出された場合にのみ表示されます。 | Reddit コンテンツをまたいでブランドイメージを向上させるための優先順位が付けられたレコメンデーションを確認します。レコメンデーションには、候補アクションと、これらの実装を担当するチームが含まれます。 |
| [引用されたセンチメント分析（Beta）](/help/dashboards/opportunities/cited-sentiment-analysis.md) | オフサイト、ソーシャル、コミュニティ | ブランド言及、センチメント、シェアオブボイス、繰り返しトピックに対するブランドプレゼンスのプロンプトセットで検出された上位の引用 URL を分析します。 | AI システムがブランドに関するプロンプトに応答する際に最も多く引用するページをまたいでブランドイメージを向上させるための優先順位が付けられたレコメンデーションを確認します。 |
| [製品カタログを拡充（ベータ版）](/help/dashboards/opportunities/enrich-product-catalog.md) | コンテンツ（オンサイト）、Adobe Commerce | 商品名や説明が一般的すぎる、専門的すぎる、または曖昧なため、LLM が解釈しにくい Commerce カタログの商品を特定します。評価対象の PDP、エージェントトラフィックのコンテキスト、および AI が生成した説明文の拡充内容を表示します。 | 提案された商品名と商品説明を確認して編集した後、最適化をデプロイして更新内容を Adobe Commerce カタログに直接公開します（修正された提案からロールバックできます）。 |
| [商品詳細ページを拡充](/help/dashboards/opportunities/enrich-product-detail-pages.md) | GEO の技術的最適化、Adobe Commerce | Adobe Commerce ストアフロントの場合、完全なカタログデータを、AI エージェントが各製品詳細ページ（PDP）でアクセスできるデータと比較します。エージェントトラフィックによって優先順位付けされた、エージェントがアクセスできる HTML からバリアント、仕様、属性、関連カタログフィールドが欠落している PDP を特定します。 | AI エージェントから見えないカタログ情報のうち復元可能なものと、その情報が LLM 主導の商品検索で重要となる理由を示します。Edge での最適化を使用してデプロイすると、CDN エッジで AI エージェント向けに完全に事前レンダリングされた HTML スナップショットがエージェントトラフィックに提供されるため、CMS やカタログを変更することなく、AI エージェントはカタログから豊富な商品情報を取得できます。 |

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
