---
title: BYOCDN ログ転送の概要
description: LLM Optimizerでエージェンティックトラフィックデータを収集するために、プロバイダーからAdobeのS3 バケットにCDN ログを転送する方法について説明します。
feature: Agentic Traffic
source-git-commit: b6e74e8706c4074a47cc355cb5f3a69a817f8a49
workflow-type: tm+mt
source-wordcount: '215'
ht-degree: 2%

---


# BYOCDN ログ転送の概要 {#cdn-log-forwarding}

顧客管理CDN （BYOCDN）のログ転送とは、LLM Optimizerがエージェント型トラフィックデータを収集および分析できるように、CDN アクセスログをAdobeのAmazon S3 バケットに送信するプロセスです。 CDN ログ転送がなければ、[Agentic Traffic](/help/dashboards/agentic-traffic.md) ダッシュボードに指標を表示できません。

以下のガイドは、同じ2段階のワークフローに従います。

1. **LLM Optimizerでのオンボード** – 必要なS3資格情報とパスの詳細を生成するには、[CDN Configuration](/help/dashboards/customer-configuration.md) ページでCDNを登録します。
2. **CDN**&#x200B;を設定します。これらの詳細を使用して、CDN プロバイダーのコンソールでログ転送ジョブを作成するか、ログを手動でアップロードします。 CloudFrontの場合、**AWS CLI**&#x200B;でのみコンソールを使用するか、配信の設定を完了できます。[CloudFront （AWS CLI） &#x200B;](/help/overview/log-forwarding/cloudfront-cli.md)を参照してください。

## CDN プロバイダー {#cdn-providers}

CDN プロバイダーの対応するガイドに従います。

| CDN プロバイダー | ガイド |
|---|---|
| Akamai | [&#x200B; ガイドを表示](/help/overview/log-forwarding/akamai.md) |
| Cloudflare | [&#x200B; ガイドを表示](/help/overview/log-forwarding/cloudflare.md) |
| CloudFront （コンソール） | [&#x200B; ガイドを表示](/help/overview/log-forwarding/cloudfront.md) |
| CloudFront （AWS CLI） | [&#x200B; ガイドを表示](/help/overview/log-forwarding/cloudfront-cli.md) |
| Fastly | [&#x200B; ガイドを表示](/help/overview/log-forwarding/fastly.md) |
| インペルバ | [&#x200B; ガイドを表示](/help/overview/log-forwarding/imperva.md) |
| その他（手動/サポートされていないCDN） | [&#x200B; ガイドを表示](/help/overview/log-forwarding/other.md) |

>[!NOTE]
>
>上記に記載されていないCDN プロバイダーの場合は、**その他（手動/サポートされていないCDN）** ガイドを使用してください。このガイドでは、手動アップロード、アドホックスクリプト、およびネイティブでサポートされていないCDNについて説明しています。
