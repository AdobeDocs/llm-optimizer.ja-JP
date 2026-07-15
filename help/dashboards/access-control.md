---
title: アクセス制御
description: Adobe LLM Optimizer での製品割り当てユーザーと組織ユーザーの違い、読み取り専用ユーザーが UI で表示する内容、管理者が Adobe Admin Console でアクセス権を割り当てる方法について説明します。
feature: Customer Configuration
autotag-review: '2026-07-15T16:44:26.227Z'
TQID: 'https://experienceleague.adobe.com/km1BB-gqTl1U92LhHxbXtoH4MTA2tLXS3mPx5u9rEoQ'
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2: id: d622681e-b12a-44e4-b49f-91c12f18b52b
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: cdd65e7e-8839-44a2-bc21-0e03623b5dd1id: e1e0219c-f879-479f-8427-888ed2a6e9c2id: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 2705cf26faea9c09817bbdcec4b4c531552df7ba
workflow-type: tm+mt
source-wordcount: 618
ht-degree: 100%

---


# アクセス制御

Adobe LLM Optimizer は、ユーザーペルソナに基づいて、基本的なアクセス制御をサポートします。 この機能は&#x200B;**有料顧客**&#x200B;のみが使用でき、リクエストに応じて有効になります。 体験版のお客様は使用できません。

>[!IMPORTANT]
>
>この機能へのアクセス権をリクエストするには、有料のお客様は、アドビアカウントマネージャーにお問い合わせください。

## 製品割り当てユーザー {#product-assigned-users}

製品に割り当てられている場合、標準の組織ユーザーと同じ機能に加え、次の権限が付与されます。

* [顧客設定](/help/dashboards/customer-configuration.md)でのプロンプト、カテゴリ、トピックおよび関連設定への書き込みアクセス権。
* [Edge での最適化](/help/dashboards/optimize-at-edge/overview.md)の最適化のデプロイと、候補の管理。
* Google Search Console 設定の管理。
* Edge での最適化とCDN の設定の管理。
* 新しいサイトのオンボーディング。

## 組織ユーザー {#organizational-users}

組織ユーザーとは、製品に割り当てられてい&#x200B;**ない**&#x200B;標準ユーザーのことです。 組織ユーザーの場合、[LLM Optimizer ダッシュボード](/help/dashboards/dashboards-overview.md)と関連ビューへのアクセス権は&#x200B;**読み取り専用**&#x200B;です。 次の制限が適用されます。

### 顧客設定 {#customer-configuration-restrictions}

* **プロンプトをアップロード**&#x200B;は無効です。
* プロンプト、カテゴリ、トピック、地域の管理と編集は無効です。

  ![読み取り専用ユーザーに対する顧客設定の制限](/help/dashboards/assets/access-control-customer-configuration.png)

### CDN 設定（顧客設定） {#cdn-configuration-restrictions}

* **CDN をオンボード**&#x200B;は無効です（読み取り専用ユーザーは CDN プロバイダーを追加できません）。
* **CDN を削除**&#x200B;は無効です（読み取り専用ユーザーは既存の CDN 設定を削除できません）。
* CDN オンボードダイアログの「**送信**」ボタンは無効です（読み取り専用ユーザーは CDN 設定を完了できません）。

  ![読み取り専用ユーザーの CDN 設定制限](/help/dashboards/assets/access-control-cdn-configuration.png)

### ブランドプレゼンス - データインサイト {#brand-presence-restrictions}

* トピックの横にある「**削除**」ボタンは非表示です（読み取り専用ユーザーは、トラッキングからトピックを削除できません）。
* プロンプトの横にある「**削除**」ボタンは非表示です（読み取り専用ユーザーは、トラッキングからプロンプトを削除できません）。

  ![読み取り専用ユーザーの非表示になっているブランドプレゼンスアクション](/help/dashboards/assets/access-control-brand-presence.png)

### エージェントトラフィック機会（エラーページ機会） {#agentic-opportunities}

404、403、503 エラーページなどの機会の場合：

* **最適化をデプロイ**&#x200B;は非表示です。
* 情報アラートには、デプロイアクセス権が必須であることが説明されています。

  ![エージェントトラフィック機会で非表示になっている最適化をデプロイ](/help/dashboards/assets/access-control-agentic-deploy.png)

### その他のオポチュニティページ {#other-opportunities}

読み取り専用の動作は、次のようなオポチュニティタイプにも適用されます。

* 目次
* 要約
* 読みやすさ
* プリレンダリング
* 見出し
* FAQ
* 欠落している構造化データ
* 一般的なパッチオポチュニティ

これらのページの場合：

* ユーザーがデプロイアクセス権を持っていない場合、**最適化をデプロイ**&#x200B;は非表示です。
* インラインアラートには、デプロイアクセス権が必須であることが説明されています。 メッセージは次のような内容です。*デプロイアクセス権は必須です - 最適化をデプロイする権限や、候補を管理する権限がありません。 アクセス権をリクエストするには、管理者にお問い合わせください。*
* デプロイアクションを含むスティッキーな下部のバーは非表示です。

  ![デプロイアクセス権が必要な場合のインラインアラート](/help/dashboards/assets/access-control-deploy-alert.png)

  ![読み取り専用ユーザーの非表示になっている Edge で最適化デプロイアクション](/help/dashboards/assets/access-control-optimize-at-edge.png)

### Google Search Console プロンプト設定 {#gsc-restrictions}

* 管理および接続アクションは無効または非表示です。
* プロンプトの追加に使用されるアクション列は非表示です。

  ![Google Search Console の設定制限](/help/dashboards/assets/access-control-gsc.png)

### 新しいサイトのオンボーディング {#onboarding-restrictions}

* アクセス制御を持たないユーザーの場合、新しいサイトのオンボーディングが無効です。

  ![新しいサイトのオンボーディングが無効](/help/dashboards/assets/access-control-onboarding.png)

## ユーザーまたはグループへの製品の割り当て {#assign-product}

組織の&#x200B;**システム管理者**&#x200B;は、[Adobe Admin Console](https://adminconsole.adobe.com/) を使用して、Adobe LLM Optimizer をユーザーまたはグループに割り当てることができます。

1. 組織の管理者権限を持つアカウントで [Adobe Admin Console](https://adminconsole.adobe.com/) にログインします。
1. 製品割り当て機能を受け取る必要があるユーザーまたはグループに、Adobe LLM Optimizer 製品プロファイル（または組織の同等の製品使用権限）を割り当てます。

手順について詳しくは、[Admin Console での製品の管理](https://helpx.adobe.com/jp/enterprise/using/manage-products.html)および[ユーザーグループの管理](https://helpx.adobe.com/jp/enterprise/using/user-groups.html)を参照してください。

>[!NOTE]
>
>Adobe Admin Console の画面フローは、リリースごとに変更される場合があります。 上記のオプションがお使いのコンソールと一致しない場合は、Adobe Admin Console の製品内ヘルプリンクを使用するか、アドビのアカウントチームにお問い合わせください。
