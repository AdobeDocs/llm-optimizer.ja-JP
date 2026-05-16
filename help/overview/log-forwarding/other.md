---
title: ログ転送 – その他（手動アップロード）
description: サポートされていないCDN プロバイダーを使用する場合に、LLM OptimizerでAgentic Traffic Data Collection用にAdobeのS3 バケットにCDN ログを手動でアップロードする方法について説明します。
feature: Agentic Traffic
autotag-review: '2026-05-15T17:54:15.685Z'
TQID: 'https://experienceleague.adobe.com/YBfhS4oM0qYRkFvS3zPzzcFAeLNBucRH5QmMBUH8h4E'
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2: id: d23587d6-14d6-4e3f-9ee1-cc18623832e1
topic_v2: id: d3cdead0-685a-4489-9250-4bb709942f66
source-git-commit: 564171851fdccee43afd233da143d66182464889
workflow-type: tm+mt
source-wordcount: 670
ht-degree: 2%

---


# ログ転送：その他（手動アップロード） {#log-forwarding-other}

**その他のBYOCDN** プロビジョニング メソッドは、次の場合にCDN ログをLLM Optimizerに提供する場合に使用するキャッチオール オプションです。

- **手動アップロード**&#x200B;をお勧めします。例えば、運用チームはログをエクスポートして定期的にアップロードします。
- **アドホックの自動プロセス**&#x200B;が使用されます（単発スクリプト、スケジュールされた書き出し、サーバーレスジョブ）。
- お客様は、組み込みのログ転送統合でネイティブにサポートされていない&#x200B;**CDNを使用しています。**

この方法は、「継続的転送」モデルを模倣しています。ログが生成され、想定されるS3の場所にアップロードされ、最終的には取り込みパイプラインによって自動的に処理されます。

## ステップ 1: LLM Optimizerでのオンボーディング {#step-1}

[LLM Optimizer](https://llmo.now/)に：

1. **設定**&#x200B;に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN設定**」タブをクリックします。

   ![CDN設定タブ ](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)

1. 「**基本を学ぶ**」をクリックします。

   <!-- ![Onboard CDN button](/help/overview/assets/log-forwarding/common/onboard-cdn-button.png)-->

1. 「**AI トラフィックインサイトをアクティブ化**」の横にある「**設定**」をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)

1. **その他**&#x200B;を選択します。

   ![その他](/help/overview/assets/log-forwarding/other/other-select.png)を選択

1. 「**オンボード**」をクリックします。

<!--   ![Onboard button](/help/overview/assets/log-forwarding/common/onboard-button.png)-->

## 手順2：ログの準備とアップロード {#step-2}

### 必要なログ形式（JSON行） {#log-format}

ログは、改行で区切られたJSON （**行ごとに1つのJSON オブジェクト**）としてアップロードする必要があります。 各ログ行には、次のフィールド **を下のスペルとまったく同じように含める必要があります**。

#### フィールドごとのスキーマ {#schema}

| フィールド | 種類 | 説明 | 例 |
|---|---|---|---|
| **timestamp** | String | **ISO 8601**&#x200B;形式に従うリクエストのタイムスタンプ。 | `"2025-02-01T23:00:05Z"` |
| **host** | String | クライアントが要求したweb ドメイン。 | `"www.example.com"` |
| **URL** | String | **パス**&#x200B;と&#x200B;**クエリパラメーター**&#x200B;が必要ですが、ドメインには&#x200B;**not**&#x200B;を含める必要があります。 | `"/home?utm_source=google"` |
| **request_method** | String | HTTP リクエストメソッド。HTTP動詞と呼ばれることもあります。 | `"GET"` |
| **request_user_agent** | String | HTTP User-Agent リクエストヘッダー。 | `"Mozilla/5.0 (compatible; GPTBot/1.0"` |
| **request_referer** | String | HTTP Referer リクエストヘッダー（空にすることができます）。 | `"https://chatgpt.com"` |
| **response_status** | 整数 | HTTP応答ステータスコード。 | `200` |
| **response_content_type** | String | HTTP Content-Type レスポンスヘッダー。 | `"text/html; charset=utf-8"` |
| **time_to_first_byte** | 整数 | サーバーへの接続を作成してから、web ページのコンテンツを&#x200B;**ミリ秒単位でダウンロードするまでの時間**。 不明または使用できない場合は0に設定します。 | `42` |

#### ログ行の例 {#example}

次の例は、3つのログ行を示しています。

```json
{"timestamp":"2025-02-01T23:06:14Z","host":"www.example.com","url":"/products/llm-optimizer?utm_source=google","request_method":"GET","request_user_agent":"Mozilla/5.0 (compatible; GPTBot/1.0; +https://openai.com/gptbot)","response_status":200,"request_referer":"","response_content_type":"text/html; charset=utf-8","time_to_first_byte":198}
{"timestamp":"2025-02-01T23:19:32Z","host":"www.example.com","url":"/services/ai-consulting/overview","request_method":"GET","request_user_agent":"PerplexityBot/1.0 (+https://www.perplexity.ai/perplexitybot)","response_status":200,"request_referer":"","response_content_type":"text/html; charset=utf-8","time_to_first_byte":255}
{"timestamp":"2025-02-01T23:44:05Z","host":"www.example.com","url":"/products/pricing/enterprise?utm_medium=social","request_method":"GET","request_user_agent":"ClaudeBot/1.0 (+https://www.anthropic.com)","response_status":200,"request_referer":"","response_content_type":"application/pdf","time_to_first_byte":312}
```

### 重要な免責事項（スペルとタイプ） {#disclaimer}

取り込みパイプラインと集計パイプラインは、**フィールド名とデータタイプ**&#x200B;について厳密です。

- フィールド名は&#x200B;**正確に**&#x200B;に一致する必要があります（大文字と小文字が区別されます）。
- データタイプは、次のように正しくなければなりません。
   - **タイムスタンプ**&#x200B;は、**ISO 8601**&#x200B;形式の文字列である必要があります。 UNIXのようなタイムスタンプが機能しない場合があります。
   - **response_status**&#x200B;は整数である必要があります。
   - **time_to_first_byte**&#x200B;は整数で、ミリ秒を使用する必要があります。
   - 文字列は有効なJSON文字列である必要があります。
- 形式が正しくない、またはフィールドが見つからない/正しくない場合は、ログがスキップされるか、解析に失敗し、レポートにデータが欠落する可能性があります。

### アップロードの場所と処理の頻度 {#upload-location}

#### パスルール {#path-rule}

次の形式を使用して、適切なフォルダーパスの下にログをアップロードします：**`yyyy/mm/dd/`** （スラッシュ付き）。

2025年2月1日UTCからのサンプルログ：`ABC123AdobeOrg/raw/byocdn-other/2025/02/01/`

#### 処理ルール {#processing-rule}

- 特定の&#x200B;**UTC日**&#x200B;中にアップロードされたログは、そのUTC日&#x200B;**の終わり近く**&#x200B;のパイプラインで処理されます（日次実行）。
- **前の日のフォルダー** （バックフィル）にアップロードされたログは、**検出され、24時間以内に**&#x200B;処理されます。

## シナリオ {#scenarios}

### シナリオ 1: Splunk / Elasticsearchのログ – エクスポートしてS3にアップロード {#scenario-splunk}

**目標**：既存のオブザーバビリティ プラットフォームからログを取得し、S3の場所に配信します。

- Splunk/Elasticの検索イベントから必須フィールドを抽出します。
- 各イベントを、上記のスキーマ（JSON行）に従って1つのJSON オブジェクトに変換します。
- 結果のファイルを、指定されたS3 バケットと&#x200B;**現在のUTC日** パス `…/byocdn-other/yyyy/mm/dd/`にアップロードします
- ログは、UTC日の終わりまでに自動的に処理されます。

### シナリオ 2: Lambda / Azure関数 – フォーマットとS3へのアップロード {#scenario-serverless}

**目標**: サーバーレスコンピューティングを使用して、CDN ログを取得/受信し、正規化して、S3の場所に配信します。

- 関数は、顧客のソース（ログストア、キュー、BLOB ストレージなど）からログを取得します。
- 関数は、フィールドを想定されるスキーマにマッピングし、**JSON行**&#x200B;を出力します。
- 関数は出力を`…/byocdn-other/yyyy/mm/dd/`にアップロードします
- ログは、UTC日の終わりまでに自動的に処理されます。

## クイックチェックリスト {#checklist}

- **行ごとに1つのJSON オブジェクト** （JSON行）
- **指定された正確なフィールドのスペル**
- 正しいデータタイプ
- **time_to_first_byte** （ミリ秒）
- 適切なUTC フォルダーにアップロードします。**yyyy/mm/dd/** （**byocdn-other**&#x200B;内）
