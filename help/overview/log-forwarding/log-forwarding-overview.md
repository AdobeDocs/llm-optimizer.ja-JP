---
title: BYOCDN ログ転送の概要
description: LLM Optimizerでアジェンティックトラフィックデータを収集するために、プロバイダーからAdobeの S3 バケットに CDN ログを転送する方法を説明します。
feature: Agentic Traffic
source-git-commit: a1ba7684ccef9baf3452cc158fc0d6a12aa7adb8
workflow-type: tm+mt
source-wordcount: '189'
ht-degree: 3%

---


# BYOCDN ログ転送の概要 {#cdn-log-forwarding}

お客様が管理する CDN （BYOCDN）のログ転送は、CDN アクセスログをAdobeのAmazon S3 バケットに送信して、LLM Optimizerがエージェンティックトラフィックデータを収集および分析できるようにするプロセスです。 CDN ログ転送がないと、[&#x200B; エージェンティックトラフィック &#x200B;](/help/dashboards/agentic-traffic.md) ダッシュボードに指標を表示できません。

以下に示すガイドは、同じ 2 段階のワークフローに従います。

1. **LLM Optimizerへのオンボーディング** - [CDN 設定 &#x200B;](/help/dashboards/customer-configuration.md) ページで CDN を登録して、必要な S3 資格情報とパスの詳細を生成します。
2. **CDN の設定** – これらの詳細を使用して、CDN プロバイダーのコンソールでログ転送ジョブを作成（または手動でログをアップロード）します。

## CDN プロバイダー {#cdn-providers}

CDN プロバイダーに対応するガイドに従ってください。

| CDN プロバイダー | ガイド |
|---|---|
| Akamai | [&#x200B; ガイドを表示 &#x200B;](/help/overview/log-forwarding/akamai.md) |
| Cloudflare | [&#x200B; ガイドを表示 &#x200B;](/help/overview/log-forwarding/cloudflare.md) |
| CloudFront | [&#x200B; ガイドを表示 &#x200B;](/help/overview/log-forwarding/cloudfront.md) |
| Fastly | [&#x200B; ガイドを表示 &#x200B;](/help/overview/log-forwarding/fastly.md) |
| インパーバ | [&#x200B; ガイドを表示 &#x200B;](/help/overview/log-forwarding/imperva.md) |
| その他（手動/サポートされていない CDN） | [&#x200B; ガイドを表示 &#x200B;](/help/overview/log-forwarding/other.md) |

>[!NOTE]
>
>お使いの CDN プロバイダーが上記に記載されていない場合は、手動のアップロード、アドホックスクリプト、およびネイティブでサポートされていない CDN に関する **その他（手動/サポートされていない CDN）** ガイドを参照してください。
