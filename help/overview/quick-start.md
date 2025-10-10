---
title: クイックスタート
description: LLM Optimizer の使用を開始し、オンボーディングプロセスを完了する方法について説明します。
source-git-commit: e8ea9ae0d6592ea3d1e9945ec117f852112ba9d7
workflow-type: tm+mt
source-wordcount: '626'
ht-degree: 0%

---


# クイックスタート

LLM Optimizer を使い始めるには、オンボーディングプロセスを完了する必要があります。 その後、LLM Optimizerのダッシュボードに完全にアクセスでき、すべての機能を使用できるようになります。

## オンボーディングの概要

オンボーディングプロセスは、ドメインのオンボーディングから始まります。 AEM Cloud のお客様であるかどうかに応じて、プロセスが異なります。 プロセスを完了したら、CDN ログ転送の情報を指定し、最後にカテゴリ、トピックおよびプロンプトをカスタマイズする必要があります。

### 手順 1：ドメインをオンボードする

### AEM Cloud のお客様

AEM Cloud をご利用のお客様（Cloud Service/Managed Services/Edge Delivery サービス）には、[Experience Hub](https://experienceleague.adobe.com/ja/docs/experience-manager-cloud-service/content/experience-hub/experience-hub) の商品お知らせカードからLLM Optimizerを試すオプションが表示されます。

>[!NOTE]
>処理が完了するまで、新しく追加されたプロンプトはブランドプレゼンスに表示されません。 AEM Cloud のお客様（Cloud Service、Managed Services/Edge Delivery サービス）は、LLM Optimizerの無料体験版を使用できます。 200 を超えるプロンプトを使用するには、別のライセンス契約が必要です。 アクセスは「現状のまま」および「利用可能」ベースで提供され、Adobeによっていつでも変更、制限、削除される可能性があります。 詳しくは、[ アカウント担当者 ] にお問い合わせください。

![LLM Optimizer体験版 &#x200B;](/help/overview/assets/llm-trial.png)

「**LLM Optimizerを試す**」ボタンをクリックすると、[https://llmo.now](https://llmo.now) にリダイレクトされます。 その後、IMS 経由でログインする必要があります。 ログインしたら、ドメインとブランド名を指定して、オンボーディングプロセスを開始します。

![LLM Optimizer ドメイン &#x200B;](/help/overview/assets/domain.png)

>[!NOTE]
>指定したドメインは、組織のすべてのユーザーが使用するもので、変更できません。

Brand Presence Analysis をトリガーするには、カテゴリ、トピック、プロンプトを指定する必要があります。

![&#x200B; ブランドプレゼンス分析 &#x200B;](/help/overview/assets/bp-analysis.png)

さらに、トラフィック分析用に CDN ログ転送も設定する必要があります。 LLM Optimizerでは、機会を特定し、お客様が AI の可視性を高められるよう支援する規範的なレコメンデーションを提供するために、エージェンティックおよびリファラルトラフィックからのブランドプレゼンスデータおよびインサイトが必要です。

### AEM Cloud 以外のお客様

契約書に署名すると、LLM Optimizerでオンボードしたいドメインで slackbot コマンドを使用してオンボードされます。 このオンボーディングが完了すると、[https://llmo.now](https://llmo.now) 経由でLLM Optimizerにログインできるようになります。

### 手順 2：インサイトの自動事前設定

ドメインがオンボードされると、LLM Optimizerによって以下が自動入力されます。

* **カテゴリ** - ドメインに関連する幅広いコンテンツ領域。
* **トピック** - ドメインに関連付けられた大量の非ブランドキーワードに関連付けられた特定のテーマ。
* **プロンプト** - ベースライン表示を提供するクエリ（ブランド済みおよび非ブランド）。

これにより、カスタム設定や入力を追加する前でも、ブランドの可視性に関する最初のインサイトを得ることができます。

### 手順 3：カテゴリ、トピック、およびプロンプトのカスタマイズ

[&#x200B; 顧客設定ダッシュボード &#x200B;](/help/dashboards/customer-configuration.md) をクリックして、カテゴリ、トピックおよびプロンプトのカスタマイズを開始します。

![&#x200B; 顧客設定ダッシュボード &#x200B;](/help/dashboards/assets/customer-config.png)

このダッシュボードから、次の操作を実行できます。

* ビジネスの優先度に合わせた新しいカテゴリを追加します。
* 追跡が必要なカスタムトピックまたはサブトピックを入力します。
* プロンプトを作成して、特定のクエリの表示を監視します。
* メンションのエイリアスを定義して、すべてのメンションが取り込まれるようにします。
* 競合他社を正確に追跡するための競合他社のエイリアスを定義する。

### 手順 4:CDN ログ転送の情報を指定する

エージェンティックトラフィックおよびリファラルトラフィックインサイトのロックを解除するには、CDN ログ転送用の情報を指定する必要があります。 ログ転送の設定方法について詳しくは、それぞれのページを参照してください。

* [エージェンティック トラフィック](/help/dashboards/agentic-traffic.md)
* [リファラルトラフィック](/help/dashboards/referral-traffic.md#setup#cdn-setup)

### 手順 5：ダッシュボードを探索し、アクションを実行する

CDN ログ転送の情報を指定すると、次の操作を実行できます。

* [&#x200B; ブランドプレゼンス &#x200B;](/help/dashboards/brand-presence.md) ダッシュボードを表示し、可視性スコアを表示し、競合他社に対するパフォーマンスを追跡します。
* [Agentic](/help/dashboards/agentic-traffic.md) および [&#x200B; リファラルトラフィック &#x200B;](/help/dashboards/referral-traffic.md) ダッシュボードを探索します。
* [&#x200B; 商談 &#x200B;](/help/dashboards/opportunities.md) を使用して、コンテンツと技術的な改善を特定します。
* データを書き出してチームで共同作業したり、同僚に製品の使用を勧めたりします。

LLM Optimizer の機能を完全に理解するには、利用可能なすべての [&#x200B; ダッシュボード &#x200B;](/help/dashboards/dashboards-overview.md) を参照してください。
