---
title: ログ転送 – その他（手動アップロード）
description: サポートされていない CDN プロバイダーを使用する場合に、Adobeの S3 バケットに CDN ログを手動でアップロードして、LLM Optimizerでアジェンティックトラフィックデータを収集する方法を説明します。
feature: Agentic Traffic
source-git-commit: b590cd14ba7d64e56a6c972fd6090e2df9de58f6
workflow-type: tm+mt
source-wordcount: '670'
ht-degree: 2%

---


# ログ転送：その他（手動アップロード） {#log-forwarding-other}

**その他の BYOCDN** プロビジョニング方式は、次の場合に CDN ログをLLM Optimizerに提供する必要があるお客様向けの包括的なオプションです。

- **手動アップロード** をお勧めします。例えば、運用チームはログをエクスポートし、定期的にアップロードします。
- **アドホックな自動プロセス** が使用されます。1 回限りのスクリプト、スケジュールされた書き出し、サーバーレスのジョブです。
- 顧客は、組み込みのログ転送統合によってネイティブにサポートされていない **CDN** を使用しています。

この方法は、「連続転送」モデルを模倣しています。ログが作成され、期待される S3 の場所にアップロードされ、最終的には取り込みパイプラインによって自動的に処理されます。

## 手順 1:LLM Optimizerへのオンボード {#step-1}

[LLM Optimizer](https://llmo.now/):

1. **設定** に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN 設定**」タブをクリックします。

   ![CDN 設定タブ ](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)

1. **開始** をクリックします。

   <!-- ![Onboard CDN button](/help/overview/assets/log-forwarding/common/onboard-cdn-button.png)-->

1. **AI トラフィックインサイトをアクティブ化** の横にある **設定** をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)

1. **その他** を選択します。

   ![ その他を選択 ](/help/overview/assets/log-forwarding/other/other-select.png)

1. **オンボード** をクリックします。

<!--   ![Onboard button](/help/overview/assets/log-forwarding/common/onboard-button.png)-->

## 手順 2：ログの準備とアップロード {#step-2}

### 必要なログ形式（JSON 行） {#log-format}

ログは、改行で区切られた JSON （**1 行につき 1 つの JSON オブジェクト**）としてアップロードする必要があります。 各ログ行には、次のフィールドを含める必要があります **以下のスペルと完全に同じ**。

#### フィールドごとのスキーマ {#schema}

| フィールド | タイプ | 説明 | 例 |
|---|---|---|---|
| **timestamp** | String | **ISO 8601** 形式に従ったリクエストのタイムスタンプ。 | `"2025-02-01T23:00:05Z"` |
| **host** | String | クライアントが要求した Web ドメイン。 | `"www.example.com"` |
| **URL** | String | **path** と **query パラメーター** が必要ですが、ドメインは含める **必要あり** せん。 | `"/home?utm_source=google"` |
| **request_method** | String | HTTP リクエストメソッド （HTTP 動詞とも呼ばれます）。 | `"GET"` |
| **request_user_agent** | String | HTTP ユーザーエージェント要求ヘッダー。 | `"Mozilla/5.0 (compatible; GPTBot/1.0"` |
| **request_referer** | String | HTTP リファラーリクエストヘッダー（空にすることができます）。 | `"https://chatgpt.com"` |
| **response_status** | 整数 | HTTP 応答のステータスコード。 | `200` |
| **response_content_type** | String | HTTP Content-Type 応答ヘッダー。 | `"text/html; charset=utf-8"` |
| **time_to_first_byte** | 整数 | サーバーへの接続を作成してから web ページのコンテンツをダウンロードするまでの時間 **ミリ秒**。 不明または使用不可の場合は 0 に設定します。 | `42` |

#### ログラインの例 {#example}

次の例は、3 つのログラインを示しています。

```json
{"timestamp":"2025-02-01T23:06:14Z","host":"www.example.com","url":"/products/llm-optimizer?utm_source=google","request_method":"GET","request_user_agent":"Mozilla/5.0 (compatible; GPTBot/1.0; +https://openai.com/gptbot)","response_status":200,"request_referer":"","response_content_type":"text/html; charset=utf-8","time_to_first_byte":198}
{"timestamp":"2025-02-01T23:19:32Z","host":"www.example.com","url":"/services/ai-consulting/overview","request_method":"GET","request_user_agent":"PerplexityBot/1.0 (+https://www.perplexity.ai/perplexitybot)","response_status":200,"request_referer":"","response_content_type":"text/html; charset=utf-8","time_to_first_byte":255}
{"timestamp":"2025-02-01T23:44:05Z","host":"www.example.com","url":"/products/pricing/enterprise?utm_medium=social","request_method":"GET","request_user_agent":"ClaudeBot/1.0 (+https://www.anthropic.com)","response_status":200,"request_referer":"","response_content_type":"application/pdf","time_to_first_byte":312}
```

### 重要な免責事項（スペルとタイプ） {#disclaimer}

取り込みパイプラインと集計パイプラインは、**フィールド名とデータタイプ** に厳密です。

- フィールド名は、（大文字と小文字を区別して **完全に** 一致する必要があります。
- データタイプは、次のように正しく設定する必要があります。
   - **timestamp** は、**ISO 8601** 形式の文字列である必要があります。 UNIX に似たタイムスタンプは機能しない場合があります。
   - **response_status** は整数である必要があります。
   - **time_to_first_byte** は整数で、ミリ秒を使用する必要があります。
   - 文字列は、有効な JSON 文字列である必要があります。
- JSON の形式が正しくないか、フィールドが見つからないか正しくない場合、ログがスキップされたり解析に失敗したりして、レポートのデータが欠落する場合があります。

### アップロードの場所と処理サイクル {#upload-location}

#### パスルール {#path-rule}

**`yyyy/mm/dd/`** （スラッシュ付き）の形式を使用して、適切なフォルダーパスにログをアップロードします。

2025 年 2 月 1 日 UTC のログの例：`ABC123AdobeOrg/raw/byocdn-other/2025/02/01/`

#### 処理ルール {#processing-rule}

- 特定の **UTC 日** にアップロードされたログは、パイプライン **その UTC 日の終わり近く** （毎日実行）によって処理されます。
- **前日のフォルダー** （バックフィル）にアップロードされたログは、24 時間以内に **検出および処理** されます。

## シナリオ {#scenarios}

### シナリオ 1:Splunk/Elasticsearchへのログ — S3 にエクスポートしてアップロード {#scenario-splunk}

**目標**：既存の観察性プラットフォームからログを取得し、S3 の場所に配信します。

- Splunk/Elastic 検索イベントから必須フィールドを抽出します。
- 上記のスキーマに従って、各イベントを 1 つの JSON オブジェクトに変換します（JSON 行）。
- 結果のファイルを、指定された S3 バケットと **現在の UTC 日** パス：`…/byocdn-other/yyyy/mm/dd/` にアップロードします
- ログは、UTC 日の終わりまでに自動的に処理されます。

### シナリオ 2:Lambda/Azure関数 – フォーマットして S3 にアップロード {#scenario-serverless}

**目標**：サーバーレス計算を使用して、CDN ログを取得/受信し、正規化して、S3 の場所に配信します。

- 関数は、顧客のソース（ログストア、キュー、BLOB ストレージなど）からログを取得します。
- 関数は、フィールドを想定されたスキーマにマッピングし、**JSON 行** を発行します。
- この関数は、出力を次の場所にアップロードします。`…/byocdn-other/yyyy/mm/dd/`
- ログは、UTC 日の終わりまでに自動的に処理されます。

## クイックチェックリスト {#checklist}

- **1 行に 1 つの JSON オブジェクト** （JSON 行）
- **正確なフィールドのスペル** 指定どおり）
- データタイプを修正
- ミリ秒単位の **time_to_first_byte** （integer）
- **byocdn-other** の下の適切な UTC フォルダー **yyyy/mm/dd/** にアップロードします
