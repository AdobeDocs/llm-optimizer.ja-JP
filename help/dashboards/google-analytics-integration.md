---
title: Google Analyticsとの連携
description: Google Analytics 4とLLM Optimizerを連携させて、AIを活用してサイトの発見、エンゲージメント、ビジネス成果を測定する方法を、リファラルトラフィックダッシュボードで説明します。
feature: Referral Traffic
autotag-review: '2026-07-15T17:51:53.586Z'
TQID: 'https://experienceleague.adobe.com/SvWn3W6hpVsWNzfWdJFvPs94lwlKX4ufjjcXKM-6xIc'
product_v2:
  - id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2:
  - id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2:
  - id: f5a6cbd1-8a9a-4c79-a6db-ba46537f516e
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
  - id: b69b2659-1057-424e-8fc5-ed9e016dc554
source-git-commit: 2705cf26faea9c09817bbdcec4b4c531552df7ba
workflow-type: tm+mt
source-wordcount: 1169
ht-degree: 17%

---


# Google Analyticsとの連携

Google Analytics 4 （GA4）との統合により、LLM Optimizerと自社のGA4 データを連携できます。これにより、ChatGPT、Gemini、Copilot、Claude、Perplexityなどのプラットフォームで、AIを利用してインサイトの発見がどのように実際のweb サイトのエンゲージメントとビジネス成果に結びついているのかを測定できます。 GA4 プロパティを接続すると、LLM OptimizerはGA4属性のリファラルトラフィック、エンゲージメント、コンバージョンの指標をそれらのソースに引き出し、**リファラルトラフィック** ダッシュボードの&#x200B;**ビジネスへの影響** タブに表示します。

>[!IMPORTANT]
>
>GA4との連携は、有料のLLM Optimizerオファーに含まれています。 無料トライアルを利用しているお客様は、有料オファーにアップグレードするまで、GA4に接続できません。

## 事前準備 {#before-you-begin}

必要な接続を完了するには：

* 接続するGA4 プロパティで&#x200B;**ビューア**&#x200B;以上のアクセス権を持つGoogle アカウント。 プロパティレベルのアクセスは、**管理者/ プロパティアクセス管理**&#x200B;の下のGoogle Analyticsで管理されます。
* LLM Optimizerで設定を管理する権限（そうでない場合、「接続」ボタンは表示されますが無効になります）。
* LLM Optimizer オリジンからのポップアップを許可するブラウザー – Googleのサインインステップが新しいタブで開きます。

Google Cloud プロジェクトを作成する、サービスアカウントを生成する、JSON キーをアップロードする、またはプロパティ IDを入力する必要は&#x200B;**ありません**。 LLM Optimizerは、Googleの標準のOAuth同意画面を通じて接続を仲介します。

## リファラルトラフィックダッシュボードにGA4を接続する {#connect}

接続フローは、[リファラルトラフィック](/help/dashboards/referral-traffic.md)ダッシュボードから次のように開始されます。

1. LLM Optimizerで&#x200B;**リファラルトラフィック**&#x200B;を開きます。

1. 「**ビジネスへの影響**」タブを開きます。

   ![リファラルトラフィックダッシュボード、「ビジネスへの影響」タブ &#x200B;](/help/dashboards/assets/ga4-integration-01-business-impact-tab.png)

1. 「**Analytics に接続**」を選択します。 LLM Optimizerによって&#x200B;**Customer Configuration > Analytics**&#x200B;にルーティングされます。 Analytics Provider ピッカーで、**Connect Google Analytics 4**&#x200B;を選択します。

   ![顧客設定、GA4が選択されたAnalytics タブ &#x200B;](/help/dashboards/assets/ga4-integration-02-analytics-ga4-picker.png)

1. 「**アカウントを接続**」を選択します。 Googleのログイン画面に新しいブラウザータブが開きます。

   ![GA4接続に対するGoogle ログイン &#x200B;](/help/dashboards/assets/ga4-integration-03-google-sign-in.png)

1. 接続するGA4 プロパティにアクセスできるGoogle アカウントでログインします。 Googleでプロンプトが表示されたら、`See and download your Google Analytics data`権限（`analytics.readonly` スコープ）を承認します。

1. GoogleからLLM Optimizerに戻り、アカウントがアクセスできるGA4 プロパティの一覧が表示されます。 接続して送信するプロパティを選択します。

1. 「LLM Optimizer」タブに戻ります。 「Analytics」タブは、完了した接続を自動的に検出し、GA4 カードに&#x200B;**Connected** ステータスが表示されます。

### 接続後 {#after-connect}

GA4をLLM Optimizerに接続すると、次のことが行われます。

* LLM Optimizer は、**過去 4 週間の完全なカレンダー週**&#x200B;と&#x200B;**現在までのカレンダー週**&#x200B;をバックフィルします。
* バックフィル後、データは&#x200B;**前日全体**&#x200B;のデータを取得して&#x200B;**毎日**&#x200B;更新されます。

>[!NOTE]
>
>バックフィルには数時間かかる場合があります。 ビジネス影響ダッシュボードは、データが到着するにつれて段階的に入力され始めます。バックフィルの実行中にアクションは必要ありません。

再接続する場合（例えば、Google アカウントまたはGA4 プロパティのいずれかを切り替える場合）、現在のカレンダー週のみが再度埋め戻され、既に読み込まれている前の週が保持されます。

## 仕組み {#how-it-works}

### 接続モデル

この統合では、Googleの標準OAuth 2.0 ユーザー委任フローを使用します。 LLM Optimizerは、選択したGA4 プロパティを対象とした更新トークンを保存します。このトークンを使用すると、LLM Optimizerアカウントから削除するまで、Googleが代わりに（読み取り専用アクセスで） GA4 Data APIを呼び出すことができます。

### LLM トラフィックの特定方法

LLM Optimizerは、GA4自体がLLM プラットフォームに属性を持つセッションに対してのみGA4にリクエストします。 今日は、`sessionSourceMedium`が`chatgpt`、`gemini.google.com`、`copilot.microsoft.com`、`claude`または`perplexity`のいずれかに一致するセッションです。 サポートされているLLM ソースのリストは、Adobeによって管理され、時間の経過とともに拡張される可能性があります。

### 取り込まれたデータ {#data-ingested}

毎日のプルごとに、次の内容を含む集計レポートが取得されます。

**ディメンション**

| GA4 ディメンション | AIが意味すること |
| --- | --- |
| `date` | セッションの発生日。 |
| `landingPage` | 訪問者がサイトで最初に見たページです。 |
| `countryId` | 訪問者の国（GA4のIP ジオロケーションによって決定）。 |
| `deviceCategory` | モバイル/デスクトップ/タブレット。 |
| `sessionSourceMedium` | GA4によって割り当てられたLLM ソース/メディア。 |

**指標**

| GA4指標 | AIが意味すること |
| --- | --- |
| `sessions` | バケット内のセッション数。 |
| `screenPageViews` | バケット内のページビュー。 |
| `bounceRate` | 直帰したセッションの割合。 |
| `totalPurchasers` | 個別の購入者（e コマースがGA4で設定されている場合）。 |
| `transactions` | トランザクション数（e コマースが設定されている場合）。 |
| `purchaseRevenue` | 購買収益（USD）: |
| `totalRevenue` | 総収益（USD）。 |

### LLM Optimizer がこのデータを使用する仕組み

このデータを使用して、LLM OptimizerのBusiness Impact ダッシュボードのページレベルのパフォーマンス、ソースの内訳、国とデバイスの分割、時間傾向を入力します。 モデルのトレーニングにデータを使用したり、テナント外で共有したりすることはありません。

### 取り込まれないもの

ユーザーID （Googleクライアント ID、IP アドレス、デバイス ID）、セッションレベルの行、イベントレベルの行、上記に記載されている以外のカスタムディメンションや指標、GA4 オーディエンスやセグメント定義はありません。

## よくある質問 {#faq}

質問：GA4統合は体験版中に利用できますか？

いいえ。 統合は、LLM Optimizer の有料顧客のみが使用できます。

質問：Google Cloud プロジェクトまたはサービスアカウントを作成する必要がありますか？

いいえ。 接続は標準のGoogle ログインです。 LLM Optimizerは、Adobe側でGoogle OAuth クライアントを管理します。必要なのは、GA4 プロパティでビューアにアクセスできるGoogle アカウントのみです。

Q：収集または保存されるデータは何ですか？

LLM Optimizerは、生のイベントレベルのデータではなく、組織が承認したGA4 Data APIの集計指標で動作します。

Q：データはどのように取り込まれますか？

選択したプロパティのGA4 Data APIのクエリを実行する権限をLLM Optimizerに付与しています。 LLM ソースに沿ったリファラルトラフィックは、そのAPIを通じて消費されます。

Q：データが更新される頻度はどれくらいですか？

データは&#x200B;**毎日**&#x200B;更新されます（バックフィル完了後の前日まで）。

質問：生のイベントレベルのデータはLLM Optimizerに保存されますか？

いいえ。 トラフィックのパターンやトレンドを把握するには、**集計済み**&#x200B;指標のみが使用されます。

Q：完全な URL、クエリ文字列またはページコンテンツは保存されますか？

ランディングページのパスは、標準レポートの一部として取り込まれます。クエリ文字列とページコンテンツは、この統合では取り込まれません。

Q: ユーザーID （Google クライアント ID、IP アドレス、デバイス ID）は保存されますか？

いいえ。

Q：データが保持される期間はどれくらいですか？

現在、データは無期限に保存されます。

Q：データは転送中および保存時に暗号化されていますか？

現在、転送中のデータは暗号化されており、保存中のデータは暗号化されていません。 今後のアップデートで変更される場合があります。

Q：履歴データはバックフィルされますか？

はい。 設定が正常に完了すると、過去 4 週間の完全なカレンダー週と現在のカレンダー週がバックフィルされます。 [接続後](#after-connect)も参照してください。

Q：接続を解除したり、アクセスを取り消したりできますか？

はい、いつでも。 LLM OptimizerのGA4 カードから別のアカウントまたはプロパティに再接続するか、LLM Optimizerのアクセス権を[https://myaccount.google.com/permissions](https://myaccount.google.com/permissions)でGoogle アカウントから完全に取り消すことができます。 アクセスを取り消すと、新しいデータの取り込みが停止します。以前に取り込まれた集約データはLLM Optimizerに残ります。

質問：GA4 プロパティが接続されていますが、ビジネスインパクトが空です。なぜですか？

LLM Optimizerは、GA4自体がサポートされているLLM ソース/メディア（現在：ChatGPT、Gemini、Copilot、Claude、Perplexity）に属するセッションのみを取得します。 表示されているタイムウィンドウにこれらのソースからのリファラルセッションがまだGA4 プロパティに記録されていない場合、接続が正常であっても、ダッシュボードは空になります。
