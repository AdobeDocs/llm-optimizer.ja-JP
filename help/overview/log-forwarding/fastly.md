---
title: ログ転送 – Fastly
description: FastlyからAdobeにCDN ログを転送し、LLM Optimizerでエージェンティックトラフィックデータを収集する方法について説明します。
feature: Agentic Traffic
autotag-review: '2026-05-15T17:51:51.808Z'
TQID: 'https://experienceleague.adobe.com/9SH1I6ajHKLFeEWXy-NpvPm-Ylk2xBKhQro3qobVEX8'
product_v2:
  - id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2:
  - id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2:
  - id: d23587d6-14d6-4e3f-9ee1-cc18623832e1
topic_v2:
  - id: d3cdead0-685a-4489-9250-4bb709942f66
  - id: e1e0219c-f879-479f-8427-888ed2a6e9c2
source-git-commit: 564171851fdccee43afd233da143d66182464889
workflow-type: tm+mt
source-wordcount: 381
ht-degree: 4%

---


# ログ転送：Fastly {#log-forwarding-fastly}

このページでは、FastlyからAdobeのS3 バケットにCDN ログを転送して、エージェント型トラフィックデータを収集する方法について説明します。 LLM Optimizer CDN設定ページを使用して、LLM Optimizerにオンボーディングします。 オンボーディングプロセスが完了したら、このページに記載されている手順に従って、Fastly web コンソールでログ転送を設定します。

## ステップ 1: LLM Optimizerでのオンボーディング {#step-1}

LLM Optimizer ページ [https://llmo.now/](https://llmo.now/)で、次の操作を行います。

1. **設定**&#x200B;に移動します。

   ![設定ボタン](/help/overview/assets/log-forwarding/common/config-button.png)

1. 「**CDN設定**」タブをクリックします。

   ![CDN設定タブ &#x200B;](/help/overview/assets/log-forwarding/common/cdn-config-tab.png)

1. 「**基本を学ぶ**」をクリックします。
1. 「**AI トラフィックインサイトをアクティブ化**」の横にある「**設定**」をクリックします。

   ![設定](/help/overview/assets/log-forwarding/common/configure.png)
1. **Fastly （BYOCDN）**&#x200B;を選択します。

   ![Fastlyを選択](/help/overview/assets/log-forwarding/fastly/fastly-select.png)
1. 「**オンボード**」をクリックします。

## 手順2:FastlyでS3 エンドポイントを作成する {#step-2}

S3 エンドポイントを作成するには、**Fastly Campaign コントロールパネル**&#x200B;で次の操作を行います。

1. Fastly ダッシュボードで、**CDN サービス** （Compute サービスではない）に移動します。
1. **Amazon Web Services S3**&#x200B;領域で、**Create endpoint**&#x200B;をクリックします。
1. 「**Amazon S3 エンドポイントを作成**」フィールドに入力します。

| フィールド | 説明 |
| --- | --- |
| **名前** | エンドポイントの人間が読み取れる名前。 |
| **配置** | デフォルト |
| **ログ形式** | 以下の「**ログ形式文字列**」セクションに表示されているログ形式文字列を使用します。 |
| **タイムスタンプ形式** | `%Y-%m-%dT%H:%M:%S.000` |
| **バケット名** | LLM Optimizer設定ページから&#x200B;**バケット名**&#x200B;をコピーします。 ![&#x200B; バケット名](/help/overview/assets/log-forwarding/fastly/fastly-bucket-name.png) |
| **ドメイン** | LLM Optimizer設定ページから&#x200B;**Domain Name**&#x200B;をコピーします。 ![&#x200B; ドメイン名](/help/overview/assets/log-forwarding/fastly/fastly-domain-name.png) |
| **アクセス方法** | **ユーザー資格情報** |
| **ユーザー資格情報** | LLM Optimizerの設定ページから&#x200B;**アクセスキー**&#x200B;と&#x200B;**秘密鍵**&#x200B;をコピーします。 ![&#x200B; アクセスキー](/help/overview/assets/log-forwarding/common/access-keys.png) |
| **期間** | `300` |

**ログ形式文字列：**

```json
{ "timestamp": "%{strftime(\{"%Y-%m-%dT%H:%M:%S%z"\}, time.start)}V", "host": "%{if(req.http.Fastly-Orig-Host, req.http.Fastly-Orig-Host, req.http.Host)}V", "url": "%{json.escape(req.url)}V", "request_method": "%{json.escape(req.method)}V", "request_referer": "%{json.escape(req.http.referer)}V", "request_user_agent": "%{json.escape(req.http.User-Agent)}V", "response_status": %{resp.status}V, "response_content_type": "%{json.escape(resp.http.Content-Type)}V", "client_country_code": "%{client.geo.country_name}V", "time_to_first_byte": "%{time.to_first_byte}V" }
```

>[!WARNING]
>
>パスワードマネージャーは、**秘密鍵** フィールドにFastly パスワードを自動入力できます。 AWSとの連携に失敗した場合は、シークレットキーを手動で入力します。

上記の手順を完了したら、**詳細オプション**&#x200B;をクリックして設定します。

| フィールド | 説明 |
| --- | --- |
| **パス** | LLM Optimizer設定ページから&#x200B;**Path**&#x200B;をコピーします。 ![パス](/help/overview/assets/log-forwarding/fastly/fastly-path.png) |
| **ログ行の形式を選択** | 空白 |
| **圧縮** | Gzip |
| **冗長性レベル** | 標準 |
| **ACL** | なし |
| **サーバー側の暗号化** | なし |
| **最大バイト** | 0 |

詳細設定オプションを設定した後：

1. 「**作成**」をクリックして、エンドポイントを作成します。
1. **アクティベート** メニューから、**実稼動環境でアクティベート**&#x200B;を選択してデプロイします。

>[!NOTE]
>
>ログをS3に継続的にストリーミングし、S3のweb サイトとAPIは、アップロードが完了した後にのみファイルを利用可能にします。

### ログエントリの例 {#example}

Amazon S3にデータを送信するためのフォーマット文字列の例を次に示します。

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
