---
title: Google Analytics との統合
description: Google Analytics 4 を LLM Optimizer に接続して、リファラルトラフィックダッシュボードで AI による検出状況、サイトエンゲージメント、およびビジネス成果を測定する方法について説明します。
feature: Referral Traffic
autotag-review: '2026-07-15T17:51:53.586Z'
TQID: 'https://experienceleague.adobe.com/SvWn3W6hpVsWNzfWdJFvPs94lwlKX4ufjjcXKM-6xIc'
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2: id: f5a6cbd1-8a9a-4c79-a6db-ba46537f516e
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bdid: b69b2659-1057-424e-8fc5-ed9e016dc554
source-git-commit: 2705cf26faea9c09817bbdcec4b4c531552df7ba
workflow-type: ht
source-wordcount: 1169
ht-degree: 100%

---


# Google Analytics との統合

Google Analytics 4（GA4）との統合により、LLM Optimizer を組織の GA4 データと接続できます。これにより、ChatGPT、Gemini、Copilot、Claude、Perplexity などのプラットフォームでの AI による検出状況が、実際の web サイトエンゲージメントやビジネス成果にどのようにつながっているかを測定できます。GA4 プロパティを接続すると、LLM Optimizer は、GA4 がこれらのソースに関連付けたリファラルトラフィック、エンゲージメント、およびコンバージョン指標を取得し、**リファラルトラフィック**&#x200B;ダッシュボードの「**ビジネスへの影響**」タブに表示します。

>[!IMPORTANT]
>
>GA4 との統合は、有料の LLM Optimizer に含まれています。無料体験版を利用しているお客様は、有料プランにアップグレードするまで GA4 を接続できません。

## 事前準備 {#before-you-begin}

接続を完了するには、次のものが必要です。

* 接続する GA4 プロパティに対して、少なくとも&#x200B;**閲覧者**&#x200B;アクセス権限を持つ Google アカウント。プロパティレベルのアクセスは、Google Analytics の&#x200B;**管理者 > プロパティのアクセス管理**&#x200B;で管理されます。
* LLM Optimizer の設定を管理する権限（権限がない場合、接続ボタンは表示されますが無効になります）。
* LLM Optimizer のオリジンからのポップアップを許可するブラウザー（Google へのサインインは新しいタブで開きます）。

Google Cloud プロジェクトの作成、サービスアカウントの生成、JSON キーのアップロード、またはプロパティ ID の入力は&#x200B;**不要**&#x200B;です。LLM Optimizer は、Google の標準 OAuth 同意画面を介して接続を仲介します。

## GA4 をリファラルトラフィックダッシュボードに接続する {#connect}

接続フローは、[リファラルトラフィック](/help/dashboards/referral-traffic.md)ダッシュボードから次のように開始されます。

1. LLM Optimizer で&#x200B;**リファラルトラフィック**&#x200B;を開きます。

1. 「**ビジネスへの影響**」タブを開きます。

   ![リファラルトラフィックダッシュボードの「ビジネスへの影響」タブ](/help/dashboards/assets/ga4-integration-01-business-impact-tab.png)

1. 「**Analytics に接続**」を選択します。LLM Optimizer により、**顧客設定 > Analytics** に移動します。Analytics Provider ピッカーで、「**Google Analytics 4 を接続**」を選択します。

   ![顧客設定の「Analytics」タブ（GA4 を選択した状態）](/help/dashboards/assets/ga4-integration-02-analytics-ga4-picker.png)

1. 「**アカウントを接続**」を選択します。新しいブラウザータブで Google のログイン画面が開きます。

   ![GA4 接続用の Google ログイン画面](/help/dashboards/assets/ga4-integration-03-google-sign-in.png)

1. 接続する GA4 プロパティへのアクセス権限を持つ Google アカウントでログインします。Google から求められたら、`See and download your Google Analytics data` 権限（`analytics.readonly` スコープ）を承認します。

1. Google から LLM Optimizer に戻ると、アカウントからアクセス可能な GA4 プロパティの一覧が表示されます。接続するプロパティを選択して送信します。。

1. 「LLM Optimizer」タブに戻ります。「Analytics」タブで接続の完了が自動的に検出され、GA4 カードに **接続済み** ステータスが表示されます。

### 接続後 {#after-connect}

GA4 を LLM Optimizer に接続すると、次のようになります。

* LLM Optimizer は、**過去 4 週間の完全なカレンダー週**&#x200B;と&#x200B;**現在までのカレンダー週**&#x200B;をバックフィルします。
* バックフィル後、データは&#x200B;**前日全体**&#x200B;のデータを取得して&#x200B;**毎日**&#x200B;更新されます。

>[!NOTE]
>
>バックフィルには数時間かかる場合があります。ビジネスへの影響ダッシュボードには、データが取り込まれるにつれて順次データが表示されます。バックフィルの実行中にユーザー側で操作する必要はありません。

再接続した場合（Google アカウントまたは GA4 プロパティを切り替える場合など）は、現在の暦週のみが再度バックフィルされ、すでに読み込まれている過去の週のデータは保持されます。

## 仕組み {#how-it-works}

### 接続モデル

この連携では、Google の標準 OAuth 2.0 ユーザー委任フローを使用します。LLM Optimizer は、選択した GA4 プロパティを対象とするリフレッシュトークンを保存します。このトークンにより、Google アカウントからアクセス権を取り消すまで、LLM Optimizer はお客様に代わって（読み取り専用アクセスで）GA4 Data API を呼び出すことができます。

### LLM トラフィックの識別方法

LLM Optimizer は、GA4 が LLM プラットフォームによるものと関連付けたセッションのみを GA4 に要求します。現在、これらは `sessionSourceMedium` が `chatgpt`、`gemini.google.com`、`copilot.microsoft.com`、`claude`、または `perplexity` のいずれかに一致するセッションです。サポートされる LLM ソースの一覧は Adobe によって管理されており、今後拡充される可能性があります。

### 取り込まれたデータ {#data-ingested}

毎日のデータ取得では、次の内容を含む集計レポートを取得します。

**ディメンション**

| GA4 ディメンション | それが意味すること |
| --- | --- |
| `date` | セッションが発生した日。 |
| `landingPage` | 訪問者がサイトで最初に表示したページ。 |
| `countryId` | GA4 の IP ジオロケーションによって判定された訪問者の国。 |
| `deviceCategory` | モバイル、デスクトップ、タブレット。 |
| `sessionSourceMedium` | GA4 が関連付けた LLM のソース／メディア。 |

**指標**

| GA4 指標 | それが意味すること |
| --- | --- |
| `sessions` | その集計区分に含まれるセッション数。 |
| `screenPageViews` | その集計区分のページビュー数。 |
| `bounceRate` | 直帰したセッションの割合。 |
| `totalPurchasers` | ユニーク購入者数（GA4 で e コマースが設定されている場合）。 |
| `transactions` | トランザクション数（e コマースが設定されている場合）。 |
| `purchaseRevenue` | 購入による収益（USD）。 |
| `totalRevenue` | 総収益（USD）。 |

### LLM Optimizer におけるこのデータの使用方法

LLM Optimizer は、このデータを使用して、ビジネスへの影響ダッシュボードのページレベルのパフォーマンス、ソース別の内訳、国別およびデバイス別の内訳、ならびに時系列の傾向を表示します。データがモデルのトレーニングに使用されたり、テナント外で共有されたりすることはありません。

### 取り込まれないデータ

Google クライアント ID、IP アドレス、デバイス ID などのユーザー識別子、セッションレベルの行、イベントレベルの行、上記以外のカスタム ディメンションやカスタム指標、GA4 オーディエンスやセグメント定義は取り込まれません。

## よくある質問 {#faq}

Q：GA4 との連携は無料体験版でも利用できますか？

いいえ。 統合は、LLM Optimizer の有料顧客のみが使用できます。

Q：Google Cloud プロジェクトやサービスアカウントを作成する必要はありますか？

いいえ。 接続には、Google の標準サインインを使用します。LLM Optimizer は、Google OAuth クライアントを Adobe 側で管理します。必要なのは、GA4 プロパティに対する閲覧者アクセス権限を持つ Google アカウントだけです。

Q：どのようなデータが収集または保存されますか？

LLM Optimizer は、組織によって認可された GA4 Data API の集計指標を使用します。生のイベントレベルデータは使用しません。

Q：データはどのように取り込まれますか？

組織が LLM Optimizer に対して、選択したプロパティの GA4 Data API をクエリする権限を付与します。LLM ソースに関連付けられたリファラルトラフィックは、その API を介して取得されます。

Q：データが更新される頻度はどれくらいですか？

データは&#x200B;**毎日**&#x200B;更新されます（バックフィル完了後の前日まで）。

Q：LLM Optimizer に生のイベントレベルデータは保存されますか？

いいえ。 トラフィックのパターンやトレンドを把握するには、**集計済み**&#x200B;指標のみが使用されます。

Q：完全な URL、クエリ文字列またはページコンテンツは保存されますか？

ランディングページのパスは標準レポートの一部として取り込まれますが、この連携ではクエリ文字列やページコンテンツは取り込まれません。

Q：ユーザー識別子（Google クライアント ID、IP アドレス、デバイス ID）は保存されますか？

いいえ。

Q：データが保持される期間はどれくらいですか？

現在、データは無期限に保存されます。

Q：データは転送中および保存時に暗号化されていますか？

現在、データは転送時には暗号化されますが、保存時には暗号化されません。今後のアップデートで変更される場合があります。

Q：履歴データはバックフィルされますか？

はい。 設定が正常に完了すると、過去 4 週間の完全なカレンダー週と現在のカレンダー週がバックフィルされます。 [接続後](#after-connect)も参照してください。

Q：接続を解除したり、アクセス権を取り消したりできますか？

はい、いつでもできます。LLM Optimizer の GA4 カードから別のアカウントやプロパティに再接続するか、[https://myaccount.google.com/permissions](https://myaccount.google.com/permissions) にアクセスして、Google アカウントから LLM Optimizer のアクセス権を完全に取り消すことができます。アクセス権を取り消すと、新しいデータの取得は停止されますが、以前に取り込まれた集計データは LLM Optimizer に保持されます。

Q：GA4 プロパティは接続されていますが、ビジネスへの影響が空になっています。なぜですか？

LLM Optimizer は、GA4 自体がサポートされている LLM ソース／メディア（現在：ChatGPT、Gemini、Copilot、Claude、Perplexity）に属するセッションのみを取得します。表示されている時間枠に、これらのソースからのリファラルセッションが GA4 プロパティでまだ記録されていない場合、接続が正常であっても、ダッシュボードは空になります。
