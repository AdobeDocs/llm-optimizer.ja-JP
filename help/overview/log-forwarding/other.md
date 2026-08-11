---
title: ログ転送 - その他（手動アップロード）
description: サポートされていない CDN プロバイダーを使用している際に、LLM Optimizer でのエージェントトラフィックのデータ収集において、CDN ログをアドビの S3 バケットに手動でアップロードする方法について説明します。
feature: Agentic Traffic
autotag-review: '2026-07-15T18:08:55.588Z'
TQID: 'https://experienceleague.adobe.com/tuB95AJnFbB4O0o2BYHiRP-W0RTxzMeMLNg-5OjF-8s'
product_v2:
  - id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2:
  - id: d1956731-2adb-4bb7-8301-2b239254ac72
  - id: e0828736-236a-487b-a478-5a635455eadc
  - id: ef4e63f5-cb4d-462d-bf9a-1f617edf2a3a
subfeature_v2:
  - id: d23587d6-14d6-4e3f-9ee1-cc18623832e1
  - id: dd952468-5202-43af-a365-6e0d2e67a703
  - id: e06fae5f-830b-4222-a469-b5e148d36465
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2:
  - id: d3cdead0-685a-4489-9250-4bb709942f66
  - id: e1e0219c-f879-479f-8427-888ed2a6e9c2
  - id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 2705cf26faea9c09817bbdcec4b4c531552df7ba
workflow-type: ht
source-wordcount: 670
ht-degree: 100%

---


# ログ転送：その他（手動アップロード） {#log-forwarding-other}

**その他の BYOCDN** プロビジョニング方法は、次の場合に CDN ログを LLM Optimizer に指定するお客様向けの包括的なオプションです。

- **手動アップロード**&#x200B;を推奨する場合 - 例：運用チームがログを書き出し、定期的にアップロードする。
- **アドホック自動処理プロセス**&#x200B;を使用する場合 - 1 回限りのスクリプト、スケジュールされた書き出し、サーバーレスジョブ。
- お客様が、組み込みのログ転送統合で&#x200B;**ネイティブにサポートされていない CDN** を使用している場合。

この方法は「継続転送」モデルを模倣しています。ログが生成され、想定される S3 の場所にアップロードされ、最終的に取り込みパイプラインにより自動的に処理されます。

## 手順 1：LLM Optimizer でオンボード {#step-1}

[LLM Optimizer](https://llmo.now/) で次の手順に従います。

1. **設定**&#x200B;に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN 設定**」タブをクリックします。

   ![「CDN 設定」タブ](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)

1. 「**開始**」をクリックします。

   <!-- ![Onboard CDN button](/help/overview/assets/log-forwarding/common/onboard-cdn-button.png)-->

1. 「**AI トラフィックインサイトをアクティブ化**」の横にある「**設定**」をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)

1. 「**その他**」を選択します。

   ![「その他」を選択](/help/overview/assets/log-forwarding/other/other-select.png)

1. 「**オンボード**」をクリックします。

<!--   ![Onboard button](/help/overview/assets/log-forwarding/common/onboard-button.png)-->

## 手順 2：ログを準備してアップロード {#step-2}

### 必須ログ形式（JSON 行） {#log-format}

ログは、改行区切りの JSON 形式（**1 行に 1 つの JSON オブジェクト**）でアップロードする必要があります。 各ログ行には、次のフィールドを&#x200B;**下記に記載されているとおりに正確に**&#x200B;含める必要があります。

#### フィールドごとのスキーマ {#schema}

| フィールド | 種類 | 説明 | 例 |
|---|---|---|---|
| **timestamp** | String | **ISO 8601** 形式に従ったリクエストのタイムスタンプ。 | `"2025-02-01T23:00:05Z"` |
| **host** | String | クライアントがリクエストした web ドメイン。 | `"www.example.com"` |
| **URL** | String | **パス**&#x200B;と&#x200B;**クエリパラメーター**&#x200B;は必須ですが、ドメインは含め&#x200B;**ない**&#x200B;でください。 | `"/home?utm_source=google"` |
| **request_method** | String | HTTP リクエストメソッドは、HTTP 動詞とも呼ばれます。 | `"GET"` |
| **request_user_agent** | String | HTTP ユーザーエージェントリクエストヘッダー。 | `"Mozilla/5.0 (compatible; GPTBot/1.0"` |
| **request_referer** | String | HTTP リファラーリクエストヘッダー（空にすることができます）。 | `"https://chatgpt.com"` |
| **response_status** | 整数 | HTTP 応答ステータスコード。 | `200` |
| **response_content_type** | String | HTTP コンテンツタイプ応答ヘッダー。 | `"text/html; charset=utf-8"` |
| **time_to_first_byte** | 整数 | サーバーへの接続を確立してから web ページのコンテンツをダウンロードするまでの時間（**ミリ秒**&#x200B;単位）。 不明または使用できない場合は、ゼロに設定します。 | `42` |

#### ログ行の例 {#example}

次の例は、3 つのログ行を示しています。

```json
{"timestamp":"2025-02-01T23:06:14Z","host":"www.example.com","url":"/products/llm-optimizer?utm_source=google","request_method":"GET","request_user_agent":"Mozilla/5.0 (compatible; GPTBot/1.0; +https://openai.com/gptbot)","response_status":200,"request_referer":"","response_content_type":"text/html; charset=utf-8","time_to_first_byte":198}
{"timestamp":"2025-02-01T23:19:32Z","host":"www.example.com","url":"/services/ai-consulting/overview","request_method":"GET","request_user_agent":"PerplexityBot/1.0 (+https://www.perplexity.ai/perplexitybot)","response_status":200,"request_referer":"","response_content_type":"text/html; charset=utf-8","time_to_first_byte":255}
{"timestamp":"2025-02-01T23:44:05Z","host":"www.example.com","url":"/products/pricing/enterprise?utm_medium=social","request_method":"GET","request_user_agent":"ClaudeBot/1.0 (+https://www.anthropic.com)","response_status":200,"request_referer":"","response_content_type":"application/pdf","time_to_first_byte":312}
```

### 重要な免責事項（スペルとタイプ） {#disclaimer}

取り込みおよび集計パイプラインは、**フィールド名とデータタイプ**&#x200B;について厳格です。

- フィールド名は、（大文字と小文字、スペルが）**完全に**&#x200B;一致する必要があります。
- データタイプは、次のとおり正しく指定する必要があります。
  - **timestamp** は、**ISO 8601** 形式の文字列にする必要があります。 UNIX 形式のタイムスタンプは正しく動作しない場合があります。
  - **response_status** は整数にする必要があります。
  - **time_to_first_byte** は整数にし、ミリ秒を使用する必要があります。
  - 文字列は、有効な JSON 文字列にする必要があります。
- JSON の形式が不正であったり、フィールドが欠落／不正確であったりすると、ログがスキップされたり、解析に失敗したりして、レポートにデータが欠落する場合があります。

### アップロード場所と処理ケイデンス {#upload-location}

#### パスルール {#path-rule}

ログは、**`yyyy/mm/dd/`**（スラッシュを含む）の形式を使用して、適切なフォルダーパスにアップロードします。

2025年2月1日（UTC）のログ例：`ABC123AdobeOrg/raw/byocdn-other/2025/02/01/`

#### 処理ルール {#processing-rule}

- 特定の **UTC 日**&#x200B;中にアップロードされたログは、**その UTC 日の終わり頃**&#x200B;にパイプラインにより処理されます（毎日実行）。
- **前日のフォルダー**&#x200B;にアップロードされたログ（バックフィル）は、24 時間以内に&#x200B;**検出および処理**&#x200B;されます。

## シナリオ {#scenarios}

### シナリオ 1：Splunk／Elasticsearch のログ - 書き出して S3 にアップロード {#scenario-splunk}

**目標**：既存の確認性の高いプラットフォームからログを取得し、S3 の場所に配信します。

- Splunk／Elasticsearch のイベントから必須フィールドを抽出します。
- 上記のスキーマ（JSON 行）に従って、各イベントを 1 つの JSON オブジェクトに変換します。
- 生成されたファイルを、指定された S3 バケットと&#x200B;**現在の UTC 日**&#x200B;パス（`…/byocdn-other/yyyy/mm/dd/`）にアップロードします。
- ログは、UTC 日の終わりまでに自動的に処理されます。

### シナリオ 2：Lambda／Azure 関数 - 書式設定して S3 にアップロード {#scenario-serverless}

**目標**：サーバーレス計算を使用して CDN ログを取得／受信し、正規化して、S3 の場所に配信します。

- 関数は、顧客のソース（ログストア、キュー、Blob Storage など）からログを取得します。
- 関数は、フィールドを想定されるスキーマにマッピングし、**JSON 行**&#x200B;を出力します。
- 関数は出力を `…/byocdn-other/yyyy/mm/dd/` にアップロードします。
- ログは、UTC 日の終わりまでに自動的に処理されます。

## クイックチェックリスト {#checklist}

- **1 行あたり 1 つの JSON オブジェクト**（JSON 行）であること
- 指定された&#x200B;**フィールド名とスペルが一致**&#x200B;していること
- 正しいデータタイプであること
- **time_to_first_byte** がミリ秒単位（整数）であること
- 適切な UTC フォルダー（**byocdn-other** の下にある **yyyy/mm/dd/**）にアップロードしていること
