---
title: BYOCDN ログ転送の概要
description: LLM Optimizer でのエージェントトラフィックのデータ収集において、プロバイダーからアドビの S3 バケットに CDN ログを転送する方法について説明します。
feature: Agentic Traffic
autotag-review: '2026-05-15T17:53:26.846Z'
TQID: 'https://experienceleague.adobe.com/EPQ6GBjNXpIwYTuzj1xDKkIzuFLOWFPmu0lqSGUAX3I'
product_v2:
  - id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2:
  - id: a0b5a505-2fd7-4c3d-b61c-b557fb6f0558
  - id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2:
  - id: d23587d6-14d6-4e3f-9ee1-cc18623832e1
topic_v2:
  - id: d3cdead0-685a-4489-9250-4bb709942f66
source-git-commit: 564171851fdccee43afd233da143d66182464889
workflow-type: tm+mt
source-wordcount: 215
ht-degree: 100%

---


# BYOCDN ログ転送の概要 {#cdn-log-forwarding}

顧客管理 CDN（BYOCDN）のログ転送とは、CDN アクセスログをアドビの Amazon S3 バケットに送信し、LLM Optimizer がエージェントトラフィックデータを収集および分析できるようにするプロセスです。 CDN ログ転送がない場合、[エージェントトラフィック](/help/dashboards/agentic-traffic.md)ダッシュボードに指標を表示できません。

以下に示すガイドは、次の同じ 2 段階のワークフローに従います。

1. **LLM Optimizer でオンボード** - [CDN 設定](/help/dashboards/customer-configuration.md)ページで CDN を登録し、必要な S3 資格情報とパスの詳細を生成します。
2. **CDN を設定** - これらの詳細を使用して、CDN プロバイダーのコンソールでログ転送ジョブを作成（またはログを手動でアップロード）します。 CloudFront の場合、コンソールを使用するか、**AWS CLI** のみを使用して配信設定を完了できます。[CloudFront（AWS CLI）](/help/overview/log-forwarding/cloudfront-cli.md)を参照してください。

## CDN プロバイダー {#cdn-providers}

CDN プロバイダーの対応するガイドに従います。

| CDN プロバイダー | ガイド |
|---|---|
| Akamai | [ガイドを表示](/help/overview/log-forwarding/akamai.md) |
| Cloudflare | [ガイドを表示](/help/overview/log-forwarding/cloudflare.md) |
| CloudFront（コンソール） | [ガイドを表示](/help/overview/log-forwarding/cloudfront.md) |
| CloudFront（AWS CLI） | [ガイドを表示](/help/overview/log-forwarding/cloudfront-cli.md) |
| Fastly | [ガイドを表示](/help/overview/log-forwarding/fastly.md) |
| Imperva | [ガイドを表示](/help/overview/log-forwarding/imperva.md) |
| その他（手動／サポートされていない CDN） | [ガイドを表示](/help/overview/log-forwarding/other.md) |

>[!NOTE]
>
>上記にリストされていない CDN プロバイダーの場合は、**その他（手動／サポートされていない CDN）**&#x200B;ガイドを使用してください。このガイドでは、手動アップロード、アドホックスクリプト、ネイティブにサポートされていない CDN について説明します。
