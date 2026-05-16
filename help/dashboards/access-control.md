---
title: アクセス制御
description: Adobe LLM Optimizerにおける製品割り当てユーザーと組織ユーザーの違い、読み取り専用ユーザーのUIでの表示、および管理者によるAdobe Admin Consoleでのアクセスの割り当て方法について説明します。
feature: Customer Configuration
autotag-review: '2026-05-15T17:26:43.837Z'
TQID: 'https://experienceleague.adobe.com/hJpQQpuHBRMdKT5oKA9z0Y8H3d3p6To-n2hWKrXgZsQ'
product_v2:
  - id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2:
  - id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2:
  - id: b704f6a0-b2fb-4df0-9177-9753751004f5
topic_v2:
  - id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 7a92587197cf6a9eec6b01bd4eaeeaf1194d3088
workflow-type: tm+mt
source-wordcount: 618
ht-degree: 4%

---


# アクセス制御

Adobe LLM Optimizerは、ユーザーペルソナに基づく基本的なアクセス制御をサポートしています。 この機能は、**有料顧客**&#x200B;のみが利用でき、リクエストに応じて有効になります。 体験版のお客様は利用できません。

>[!IMPORTANT]
>
>この機能へのアクセスをリクエストするには、有料のお客様はAdobeのアカウントマネージャーにお問い合わせください。

## 製品割り当てユーザー {#product-assigned-users}

製品に割り当てられている場合は、次の権限に加えて、標準的な組織ユーザーと同じ機能を使用できます。

* プロンプト、カテゴリ、トピック、および関連する設定について、[顧客設定](/help/dashboards/customer-configuration.md)で書き込みアクセス権を設定します。
* Edge[&#128279;](/help/dashboards/optimize-at-edge/overview.md)最適化で最適化をデプロイし、提案を管理します。
* Google Search Consoleの設定を管理します。
* EdgeとCDNの設定で最適化を管理します。
* 新しいサイトのオンボーディング。

## 組織ユーザー {#organizational-users}

組織ユーザーは、製品に割り当てられた&#x200B;**not**&#x200B;の標準ユーザーです。 組織ユーザーの場合、[LLM Optimizer ダッシュボード &#x200B;](/help/dashboards/dashboards-overview.md)および関連ビューに&#x200B;**読み取り専用** アクセスできます。 次の制限が適用されます。

### 顧客設定 {#customer-configuration-restrictions}

* **アップロードプロンプト**&#x200B;は無効です。
* プロンプト、カテゴリ、トピック、地域の管理と編集は無効になっています。

  ![読み取り専用ユーザーに対する顧客設定の制限](/help/dashboards/assets/access-control-customer-configuration.png)

### CDN設定（顧客設定） {#cdn-configuration-restrictions}

* **オンボード CDN**&#x200B;は無効です（読み取り専用ユーザーはCDN プロバイダーを追加できません）。
* **削除CDN**&#x200B;は無効です（読み取り専用ユーザーは既存のCDN設定を削除できません）。
* CDN オンボードダイアログの&#x200B;**送信** ボタンが無効になっています（読み取り専用ユーザーはCDN設定を完了できません）。

  読み取り専用ユーザーの![CDN設定制限](/help/dashboards/assets/access-control-cdn-configuration.png)

### ブランドプレゼンス：データインサイト {#brand-presence-restrictions}

* トピックの横にある&#x200B;**削除** ボタンは非表示になっています（読み取り専用ユーザーはトラッキングからトピックを削除できません）。
* プロンプトの横にある&#x200B;**削除** ボタンは非表示です（読み取り専用ユーザーはトラッキングからプロンプトを削除できません）。

  読み取り専用ユーザーに対して![ブランドプレゼンスアクションが非表示になっています](/help/dashboards/assets/access-control-brand-presence.png)

### エージェント型トラフィック機会（エラーページ機会） {#agentic-opportunities}

404、403、503 エラーページなどの商談の場合：

* **デプロイ最適化**&#x200B;は非表示です。
* 情報アラートでは、デプロイメントアクセスが必要であることを説明します。

  ![&#x200B; エージェンティックトラフィック商談で非表示の最適化をデプロイする](/help/dashboards/assets/access-control-agentic-deploy.png)

### その他の商談ページ {#other-opportunities}

読み取り専用の動作は、次のような商談タイプにも適用されます。

* 目次
* 要約
* 読みやすさ
* プリレンダリング
* 見出し
* FAQ
* 構造化データがありません
* 汎用パッチオポチュニティ

これらのページの場合：

* ユーザーがデプロイ アクセス権を持っていない場合、**デプロイ最適化**&#x200B;は非表示になります。
* インラインアラートは、デプロイアクセスが必要であることを説明します。 メッセージは次のようになります。*Deploy Access Required – 最適化をデプロイしたり、提案を管理したりする権限がありません。 アクセスをリクエストするには、管理者にお問い合わせください。*
* デプロイアクションを含むスティッキーなボトムバーは非表示になっています。

  ![&#x200B; デプロイアクセスが必要な場合のインラインアラート &#x200B;](/help/dashboards/assets/access-control-deploy-alert.png)

  ![読み取り専用ユーザーに対して非表示のEdge デプロイアクションで最適化](/help/dashboards/assets/access-control-optimize-at-edge.png)

### Google Search Console プロンプト設定 {#gsc-restrictions}

* 管理および接続アクションは無効または非表示です。
* プロンプトの追加に使用されるアクション列は非表示になっています。

  ![Google Search Consoleの設定制限](/help/dashboards/assets/access-control-gsc.png)

### 新しいサイトのオンボーディング {#onboarding-restrictions}

* アクセス制御を持たないユーザーは、新しいサイトのオンボーディングが無効になります。

  ![新しいサイトのオンボーディングが無効になりました](/help/dashboards/assets/access-control-onboarding.png)

## ユーザーまたはグループへの製品の割り当て {#assign-product}

組織の&#x200B;**システム管理者**&#x200B;は、[Adobe Admin Console](https://adminconsole.adobe.com/)を使用して、Adobe LLM Optimizerをユーザーまたはグループに割り当てることができます。

1. 組織の管理者権限を持つアカウントで[Adobe Admin Console](https://adminconsole.adobe.com/)にログインします。
1. 製品割り当て機能を受け取る必要があるユーザーまたはグループに、Adobe LLM Optimizer製品プロファイル（または組織の同等の製品使用権限）を割り当てます。

詳細な手順については、[Admin Consoleでの製品の管理](https://helpx.adobe.com/jp/enterprise/using/manage-products.html)および[&#x200B; ユーザーグループの管理](https://helpx.adobe.com/jp/enterprise/using/user-groups.html)を参照してください。

>[!NOTE]
>
>Adobe Admin Consoleの画面フローは、リリース間で変化する可能性があります。 上記のオプションが本体と一致しない場合は、Adobe Admin Consoleの製品内ヘルプリンクを使用するか、Adobeのアカウントチームにお問い合わせください。
