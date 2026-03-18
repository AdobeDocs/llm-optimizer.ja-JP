---
title: ログ転送 – Fastly
description: LLM Optimizerで Fastly からAdobeの S3 バケットに CDN ログを転送して、アジェンティックトラフィックデータを収集する方法を説明します。
feature: Agentic Traffic
source-git-commit: d1f98770b39f550c36d93ece9b89933c0e90f189
workflow-type: tm+mt
source-wordcount: '381'
ht-degree: 4%

---


# ログ転送：Fastly {#log-forwarding-fastly}

このページでは、CDN ログを Fastly からAdobeの S3 バケットに転送してアジェンティックトラフィックデータを収集する方法について説明します。 LLM Optimizer CDN 設定ページを使用して、LLM Optimizerにオンボードします。 オンボーディングプロセスが完了したら、このページで説明する手順に従って、Fastly web コンソールでログ転送を設定します。

## 手順 1:LLM Optimizerへのオンボード {#step-1}

LLM Optimizerページ [https://llmo.now/](https://llmo.now/) で、次の操作を行います。

1. **設定** に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN 設定**」タブをクリックします。

   ![CDN 設定タブ ](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)

1. **開始** をクリックします。
1. **AI トラフィックインサイトをアクティブ化** の横にある **設定** をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)
1. 「**Fastly （BYOCDN）**」を選択します。

   ![Fastly を選択 ](/help/overview/assets/log-forwarding/fastly/fastly-select.png)
1. **オンボード** をクリックします。

## 手順 2:Fastly での S3 エンドポイントの作成 {#step-2}

**Fastly Campaign コントロールパネル** で S3 エンドポイントを作成するには：

1. Fastly ダッシュボードで、**CDN サービス** （Compute サービスではありません）に移動します。
1. **Amazon Web Services S3** エリアで、「**エンドポイントを作成** をクリックします。
1. **Amazon S3 エンドポイントを作成** フィールドに入力します。

| フィールド | 説明 |
| --- | --- |
| **名前** | エンドポイントの人間が読み取れる名前。 |
| **プレースメント** | デフォルト |
| **ログ形式** | 以下の **ログ形式文字列** セクションに示すログ形式文字列を使用します。 |
| **タイムスタンプ形式** | `%Y-%m-%dT%H:%M:%S.000` |
| **バケット名** | LLM Optimizerの設定ページから **バケット名** をコピーします。![バケット名 ](/help/overview/assets/log-forwarding/fastly/fastly-bucket-name.png) |
| **ドメイン** | LLM Optimizer設定ページから **ドメイン名** をコピーします。![ドメイン名 ](/help/overview/assets/log-forwarding/fastly/fastly-domain-name.png) |
| **Access メソッド** | **ユーザー資格情報** |
| **ユーザー資格情報** | **アクセスキー** と **秘密鍵** をLLM Optimizer設定ページからコピーします。![アクセスキー ](/help/overview/assets/log-forwarding/common/access-keys.png) |
| **期間** | `300` |

**ログ形式文字列：**

```json
{ "timestamp": "%{strftime(\{"%Y-%m-%dT%H:%M:%S%z"\}, time.start)}V", "host": "%{if(req.http.Fastly-Orig-Host, req.http.Fastly-Orig-Host, req.http.Host)}V", "url": "%{json.escape(req.url)}V", "request_method": "%{json.escape(req.method)}V", "request_referer": "%{json.escape(req.http.referer)}V", "request_user_agent": "%{json.escape(req.http.User-Agent)}V", "response_status": %{resp.status}V, "response_content_type": "%{json.escape(resp.http.Content-Type)}V", "client_country_code": "%{client.geo.country_name}V", "time_to_first_byte": "%{time.to_first_byte}V" }
```

>[!WARNING]
>
>パスワードマネージャーは、Fastly のパスワードを「**秘密鍵**」フィールドに自動入力する場合があります。 AWS統合に失敗した場合は、秘密鍵を手動で入力します。

上記の手順を完了したら、「**詳細設定オプション**」をクリックして、以下を設定します。

| フィールド | 説明 |
| --- | --- |
| **パス** | LLM Optimizer設定ページから **パス** をコピーします。![パス ](/help/overview/assets/log-forwarding/fastly/fastly-path.png) |
| **ログ行の形式を選択** | 空白 |
| **圧縮** | Gzip |
| **冗長性レベル** | 標準 |
| **ACL** | なし |
| **サーバー側の暗号化** | なし |
| **最大バイト数** | 0 |

詳細設定オプションの設定後：

1. **作成** をクリックして、エンドポイントを作成します。
1. **アクティベート** メニューから、デプロイする **実稼動環境でアクティベート** を選択します。

>[!NOTE]
>
>Fastly はログを S3 に継続的にストリーミングします。S3 web サイトおよび API では、アップロードが完了した後にのみファイルを使用できるようになります。

### ログエントリの例 {#example}

Amazon S3 にデータを送信する際の書式文字列の例を以下に示します。

```json
{
  "timestamp": "2026-02-10T05:05:36+0000",
  "host": "example.com",
  "url": "/my/path",
  "request_method": "GET",
  "request_referer": "https://example.com/my/other/path",
  "request_user_agent": "Mozilla/5.0 (iPhone; CPU iPhone OS 13_2_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.3 Mobile/15E148 Safari/604.1",
  "response_status": 200,
  "response_content_type": "text/css; charset=utf-8",
  "client_country_code": "argentina",
  "time_to_first_byte": "0.138"
}
```
