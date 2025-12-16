---
title: 最適化の機会
description: 機会ダッシュボードを使用して、ブランドの可視性を高めるためにサイトがどのように改善されたかを自動的に検出する方法を説明します。
feature: Opportunities
source-git-commit: 39658a057fd4d67f74dc286e1687e384133ac653
workflow-type: tm+mt
source-wordcount: '624'
ht-degree: 0%

---


# 最適化の機会

最適化の機会は、サイトや外部プレゼンスを改善して AI 検索でのブランドの可視性を高めることができる場所を示すインサイトを自動的に検出します。

これらの最適化には、ページ上の修正（構造化コンテンツ、正規化、概要の追加）、技術的な調整（AI クローラーのブロック解除やエラーの解決）、サードパーティの権限のあるサイト上のコンテンツへの影響などが含まれます。 これらの最適化の機会に対処することで、ブランドが正確に示され、ジェネレーティブな回答で引き合いに出される可能性が高くなります。

![ 最適化の機会 ](/help/dashboards/assets/oport.png)

## 商談ダッシュボード

ダッシュボードに表示される最適化機会は、プレーヤーギャップ、トレンドトピック、パフォーマンスデータに基づいて優先順位が付けられ、リストとして表示されます。 検索フィールドを使用して、特定のオポチュニティを検索できます。 また、商談はタグ別にグループ化されています。タグを直接クリックすると、そのタグの下にグループ化されたすべての商談を表示できます。

**詳細** をクリックすると、別のウィンドウが開き、詳細な情報と追加のガイダンスが表示されます。

## サポートされる商談

現在サポートされているオポチュニティの表を次に示します。

| 商談 | タイプ | 特定された問題 | 修正候補 |
|---------|----------|----------|----------|
| 長い段落の要約 | コンテンツ（オンサイト） | 推奨される長さのしきい値を超える段落を検出します。 影響を受ける URL とオーバーサイズのテキストスニペットを表示します。 | 要約を作成するか、長いテキストを短いスキャン可能なセクションに分割します。 |
| 構造化コンテンツのレコメンデーション（FAQ） | コンテンツ（オンサイト） | FAQ エントリに一致せずに、人気の高いプロンプトを検出します。 関連するプロンプト、カテゴリおよび影響を受ける URL を表示します。 | よくあるクエリに一致する簡潔な回答を含む FAQ スキーマブロックを追加しました。 |
| 不足している Hreflang の検出 | コンテンツ（オンサイト） | hreflang 属性が欠落しているページにフラグを付けます。 影響を受ける URL と想定されるカバレッジ（言語/地域別）を提供します。 | hreflang タグを実装して、正しいローカライズ版を示します。 |
| 不足している正規を検出する | コンテンツ（オンサイト） | 正規タグのないページ、または競合するタグを持つページをスキャンします。 影響を受ける URL と重複のリストを表示します。 | 各ページの優先バージョンを指す正規タグを追加します。 バリアント間で一貫した使用を確保します。 |
| 空の見出しの検出 | コンテンツ（オンサイト） | 見出しタグが存在するがテキストを含まないページにフラグを付けます。 空のタグの URL と場所を表示します。 | 見出しに説明テキストを追加し、その下のコンテンツを反映させます。 |
| 重複する見出しの検出 | コンテンツ（オンサイト） | HTMLの見出しタグをスキャンし、繰り返される見出しにフラグを付けます。 影響を受ける URL と重複したテキストスニペットを表示します。 | 見出しを改訂して一意にし、階層を維持します（H1→H2→H3）。 重複するセクションを結合または名前変更します。 |
| ブロックされた匿名トラフィックの検出 | テクニカル地域 | 既知の AI エージェント（GPTBot、PerplexityBot など）からのブロックされたリクエストの CDN ログを分析します。 影響を受ける URL とエージェントをレポートします。 | 必要に応じて robots.txt またはサーバー設定を更新し、サポートされている AI クローラーにアクセスできるようにします。 |
| 404s/403s/5xx の問題の検出 | テクニカル地域 | CDN ログでエラー応答を監視します。 頻度、影響を受ける URL、失われた推定ヒットについてレポートします。 | リンク切れを修正し、権限を更新し、サーバーサイドの問題を解決して、主要なコンテンツが 200 応答を返すようにします。 |
| コンテンツの表示の復元（早期アクセス） | テクニカル地域 | 重要なコンテンツが AI エージェントから非表示になっているページにフラグを付けます。 影響を受ける URL と、復元可能な期待されるコンテンツを表示します。 | ページを事前にレンダリングして、JavaScriptを実行しなくても AI エージェントがより多くのコンテンツを利用できるようにします。 |

## 自動最適化 {#auto-optimization}

自動最適化により、推奨される最適化をワンクリックでデプロイでき、手動の労力と価値創出までの時間を削減できます。 最適化は、コンテンツソースまたは CDN エッジで適用できます。 Edge ベースの自動最適化は、現在、一部のオポチュニティに対して早期アクセスで利用できます。 詳しくは、[Edgeで最適化 ](/help/dashboards/optimize-at-edge.md) ページを参照してください。

<!--### Recover Content Visibility Opportunity {#recover-contet}

As stated above, the content visibility opportunity, flags pages where key content is lost for AI agents due to client-side rendering. For each identified page, it shows you exactly which content is missing from the AI agent view, helping you pinpoint visibility gaps. It's also supported by an edge-based pre-rendering capability that can serve more HTML content to agentic traffic without requiring Content Management System (CMS) changes. This functionality is currently in Early Access and requires setup from the LLM Optimizer team. Please contact `llmo-at-edge@adobe.com` to activate the content visibility opportunity.-->

### 追加ツール

[LLM 表示チェッカー ](https://chromewebstore.google.com/detail/is-your-webpage-citable/jbjngahjjdgonbeinjlepfamjdmdcbcc) は、LLM がアクセスできる web ページコンテンツの量と、非表示のままの内容を正確に確認できるChrome拡張機能です。 無料のスタンドアロン診断ツールとして設計されているため、製品ライセンスやセットアップは必要ありません。 ユーザーはシングルクリックで、任意のサイトの機械可読性を評価し、AI エージェントの表示内容と人間のユーザーの表示内容を横に並べて比較できます。 また、LLM Optimizerを使用して復元できるコンテンツの量を見積もります。
