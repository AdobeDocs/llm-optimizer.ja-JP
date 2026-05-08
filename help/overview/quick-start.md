---
title: クイックスタート
description: ブランド名とドメインをオンボーディングし、Experience HubまたはExperience Cloudから体験版をアクティベートして、Adobe LLM Optimizerの設定を完了する方法について説明します。
feature: Quickstart, Onboarding
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: c0713b97-4af8-4c41-b742-5afcc6ced468
subfeature_v2: id: b70f186a-2ef9-43ce-b452-25fa1d91bcda
topic_v2: id: cdd65e7e-8839-44a2-bc21-0e03623b5dd1id: e1e0219c-f879-479f-8427-888ed2a6e9c2
autotag-review: '2026-04-30T18:12:24.085Z'
source-git-commit: 9c8e5750410f0746d1777d2637d84561d15a7a64
workflow-type: tm+mt
source-wordcount: 1472
ht-degree: 38%

---


# クイックスタート

LLM Optimizerを使い始めるには、オンボーディングプロセスを完了してください。 次に、カテゴリ、トピック、プロンプトをカスタマイズし、CDN ログ転送を設定し、完全なインサイトを得るために[ ダッシュボード ](/help/dashboards/dashboards-overview.md)を開きます。

<!--Where steps differ by layout, use **Customer Configuration (classic experience)** or **Brands Management** / **Prompts Management**, whichever matches your current interface.-->

## ブランド中心主義 {#brand-centric-experience}

デフォルトでは、新規顧客はオンボーディング主導の設定により、焦点を絞ったブランドファーストのインターフェイスから開始されます。 この新しいインターフェイスでは、各組織は1つのアクティブなブランドと、選択できる追加の推奨ブランドから始まります。 既存のLLM Optimizerのお客様は、このブランド中心の体験に徐々に移行していきます。

## オンボーディングの概要

オンボーディングプロセスは、ドメインとブランド名をオンボーディングすることから始まります。 以下では、オンボーディングジャーニーの各部分と、できるだけ早くLLM Optimizerを使い始めるためのヒントについて詳しく説明します。

### Adobe LLM Optimizer で公開ページにアクセスできるようにする

正確なコンテンツと技術的なレコメンデーションを提供するには、Adobe LLM Optimizer で公開ページにアクセスする必要があります。 これは、安全な内部 web クローラー（Spacecat/1.0 ユーザーエージェント）を通じて実現されます。

設定要件：

* サイトのrobots.txt ファイルまたはbot-traffic管理ルールで、Spacecat/1.0 ユーザーエージェントを許可リストに追加します。
* ページがドメインレベルまたはCDN レベルでブロックされていないことを確認します。 ブロックされたページでは、インデックスを作成できません。つまり、これらのページに対して最適化タスクとインサイトを生成できません。

ダッシュボードでコンテンツの可視性が低いと表示される場合は、web クローラーがドメインにアクセスできることを確認してください。 制限付きアクセスは、インデックス作成が不完全になる一般的な原因です。

## ステップ 1：ブランド名とドメインのオンボーディング {#step-1-onboard-your-domain}

LLM Optimizerを使い始めるには、まず体験版をアクティベートし、ブランド名とドメインをオンボーディングします。

### 体験版を有効にする

アクティベーションフローは、Adobeの製品によって異なります。

#### AEM Cloudのお客様

AEM Cloudのお客様は、体験版をアクティベートするために、次のいずれかを実行できます。

* [Experience Hub](https://experienceleague.adobe.com/ja/docs/experience-manager-cloud-service/content/experience-hub/experience-hub)に移動し、お知らせカードを使用してLLM Optimizerを有効にします。 **LLM Optimizerを試す**&#x200B;を選択すると、[https://llmo.now](https://llmo.now)にリダイレクトされます。 IMSを介してログインし、ドメインとブランド名を入力してオンボーディングプロセスを開始します。
* または、[https://llmo.now](https://llmo.now)に直接移動してログインします。

![LLM Optimizer 体験版](/help/overview/assets/llm-trial.png)

#### Adobe AnalyticsとAdobe Customer Journey Analytics

Adobe AnalyticsとAdobe Customer Journey Analyticsをご利用のお客様の場合は、Experience Cloud ホームページにバナーが表示されます。

![Experience Cloudのホームページと「Adobe LLM Optimizer体験版を開始」バナー](/help/overview/assets/experience-cloud-llmo-trial-banner.png)

体験版は、次のいずれかの方法でアクティベートできます。

* バナーで「**Adobe LLM Optimizer体験版を開始**」を選択します。
* [https://llmo.now](https://llmo.now)に直接移動してログインします。

体験版が有効になったら、ブランド名とドメインのオンボーディングを続行します。

>[!NOTE]
>
> * **無償体験版：** AEM CloudおよびAdobe Analytics/Customer Journey Analyticsのお客様は、無償体験版のLLM Optimizerを使用できます。
> * **2026年4月1日以降に体験版をアクティベートするお客様**&#x200B;は、最大100個のプロンプト、1つのドメインを使用でき、1つの機会タイプに対して最大10個のURLに最適化をデプロイできます。
> * **2026年4月1日より前に体験版をアクティベートしたお客様**&#x200B;は、引き続き、既存の条件に従って最大200個のプロンプトにアクセスできます。
>
>含まれている制限を超えて使用するには、別個のライセンス契約が必要です。 アクセスは「現状のまま」および「利用可能な状態」で提供され、いつでも変更、制限、または削除することができます。 詳細については、アカウント担当者にお問い合わせください。

#### ブランド名とドメインのオンボーディング

LLM Optimizerを使用するには、ブランド名とドメインをオンボーディングする必要があります。

1. ブランド名と関連ドメインを入力します。

   * これは、コンテンツを分析して最適化するメインドメインです。

1. オンボーディングの完了。

   * 送信されたデータは、LLM Optimizerでドメインの分析とインサイトの生成に利用されます。

![LLM Optimizer ドメイン](/help/overview/assets/domain.png)

>[!NOTE]
>新しく追加されたプロンプトは、処理が完了するまで[ブランドプレゼンスダッシュボード](/help/dashboards/brand-presence.md)に表示されません。

>[!NOTE]
>指定したドメインは、組織内のすべてのユーザーによって使用され、変更できません。

オンボーディングフェーズ中に、カテゴリ、トピック、プロンプトの小さなセットが生成されます。 これらのプロンプトでのブランドプレゼンス分析は、サイトをオンボードした直後に使用できるようになります。

エッジで最適化をデプロイする機能も利用できます。 詳しくは、[Edgeでの最適化に関するよくある質問](https://experienceleague.adobe.com/ja/docs/llm-optimizer/using/resources/optimize-at-edge/overview#frequently-asked-questions)を参照してください。

さらに、トラフィック分析用に[CDN ログ転送](#step-4)を設定します。 LLM Optimizerなら、AI エージェントとリファラルトラフィックからブランドプレゼンスデータとインサイトを獲得し、ビジネス機会を特定して、AIによる可視性を強化するための推奨事項を処方できます。

### AEM Cloud以外のお客様

組織が業務契約書を完成させた後、選択したドメインでLLM Optimizerにオンボーディングされます。 オンボーディングが完了したら、[https://llmo.now](https://llmo.now)でログインします。

## 手順 2：カテゴリ、トピック、プロンプトをカスタマイズ {#step-2-customize-categories-topics-and-prompts}

サイトにオンボードしたら、オンボーディングフェーズ中に自動生成された小さなセットのプロンプトに基づいて、ブランドプレゼンス分析を表示できます。 今後、ブランドのカテゴリー、トピック、プロンプトをカスタマイズできるようになります。

### 顧客設定（従来のナビゲーション）

従来のナビゲーション（ブランド中心のエクスペリエンスではなく）を使用している場合は、[顧客設定ダッシュボード ](/help/dashboards/customer-configuration.md)から、ブランドのカテゴリ、トピック、プロンプトをカスタマイズできます。

![顧客設定ダッシュボード](/help/overview/assets/prompt-creation.png)

顧客設定ダッシュボードでは、次のことができます。

* ビジネスの優先度に合わせて&#x200B;**新しいカテゴリ**&#x200B;を追加します。 カテゴリには、ドメインに関連する幅広いコンテンツ領域を指定できます。
* 追跡する&#x200B;**カスタムトピック**&#x200B;またはサブトピックを入力します。 トピックには、ドメインに関連付けられた大量のブランド化以外のキーワードに結び付けられた特定のテーマを指定できます。
* 特定のクエリの可視性を監視する&#x200B;**プロンプト**&#x200B;を作成します。 プロンプトは、ベースラインの可視性を指定するクエリ（ブランド化およびブランド化以外）です。 指定したカテゴリとトピックに基づいて、制限された数のプロンプトのみが自動的に生成されます。
* ブランドに関するすべての言及が確実に取得および反映されるように、言及&#x200B;**エイリアス**&#x200B;を定義します。
* その他のブランドを正確に追跡する&#x200B;**その他のエイリアス**&#x200B;を定義します。

>[!NOTE]
>LLM に尋ねる正確なプロンプトは、LLM によって開示されていないので、公開されていません。

>[!NOTE]
>
> カテゴリ、トピック、プロンプトを設定する方法について詳しくは、[カテゴリ、トピック、プロンプトの設定のベストプラクティス](/help/overview/best-practices-topics-prompts.md)ページを参照してください。

### ブランド中心のエクスペリエンスのカテゴリ、トピック、プロンプト

ブランド中心主義のエクスペリエンスを利用している顧客に対しては、次のようにカテゴリ、トピック、プロンプトを追加できます。

* **カテゴリ** — **ブランド管理**&#x200B;に移動し、**カテゴリ**&#x200B;をクリックします。 カテゴリーはグローバルレベルで定義され、ブランド管理の下のすべてのブランドに適用されます。

  ![ ナビゲーションにカテゴリを含むブランド管理](/help/assets/brand-centric-experience/llmo-app-shell.png)

* **トピックとプロンプト** — **プロンプト管理**&#x200B;に移動して、特定のブランドのプロンプトを含むトピックとプロンプトを作成します。

  ![ プロンプト管理](/help/assets/brand-centric-experience/prompts-management.png)

## 手順 3：ブランドプレゼンスインサイト

ドメインをオンボードした後、オンボーディング中に自動的に生成されたプロンプトに基づいて、ブランドプレゼンスビューに初期インサイトが表示されます。 独自のカテゴリ、トピック、プロンプトをカスタマイズすると、LLM Optimizer では指定したプロンプトに基づいてブランドプレゼンス分析を自動的にトリガーし、結果は 24 時間以内に使用できるようになります。

>[!NOTE]
>
> ブランド中心のエクスペリエンスを利用しているお客様の場合は、**ブランドプレゼンス**&#x200B;に移動し、ブランド ドロップダウンを使用するためのブランドプレゼンスを表示するブランドを選択します。 このエクスペリエンスを使用すると、**すべてのブランド** レベルでブランドの可視性を表示することもできます。

## 手順 4：CDN ログ転送の情報を指定 {#step-4}

エージェント型トラフィックとリファラルトラフィックインサイトを活用するには、CDN ログ転送を登録して、LLM Optimizerがアクセスログを読み取れるようにします。

### 顧客設定（従来のナビゲーション）

従来のナビゲーションを使用している場合は、[顧客設定ダッシュボード ](/help/dashboards/customer-configuration.md#cdn-configuration)からCDN ログ転送情報を追加できます。 「**CDN設定**」タブを開き、**オンボード CDN**&#x200B;を選択します。

![顧客設定 CDN](/help/overview/assets/cc-cdn.png)

または、CDN プロバイダーが事前に追加されていない場合（上記の説明に従って）、エージェントトラフィックダッシュボードとリファラルトラフィックダッシュボードに初めてアクセスする際に、CDN ログ転送を追加するプロンプトが表示されます。 詳しくは、次を参照してください。

* [エージェントトラフィック](/help/dashboards/agentic-traffic.md#cdn-setup)
* [リファラルトラフィック](/help/dashboards/referral-traffic.md#setup)

>[!NOTE]
>顧客管理CDN （BYOCDN）を使用する際のログ転送について詳しくは、[BYOCDN ログ転送の概要](/help/overview/log-forwarding/log-forwarding-overview.md)を参照してください

### Brand Centric Experience CDN ログ転送

Brand Centric エクスペリエンスを利用しているお客様の場合、**Brands Management**&#x200B;からCDN ログ転送情報を追加できます。**Brands Management**&#x200B;を開き、**CDN** ラベルをクリックします。

![ ブランド管理 – CDN ログ転送](/help/assets/brand-centric-experience/brands-management-cdn.png)

## 手順 5：ダッシュボードを探索し、アクションを実行

CDN ログ転送の情報を指定すると、次の操作を実行できます。

* [ブランドプレゼンス](/help/dashboards/brand-presence.md)ダッシュボードを表示して、可視性スコアを確認し、その他のブランドと比較したパフォーマンスを追跡します。
* CDN ログ転送が設定されている場合は、[Agentic](/help/dashboards/agentic-traffic.md)および[リファラルトラフィック](/help/dashboards/referral-traffic.md) ダッシュボードを探索します。
* [オポチュニティ](/help/dashboards/opportunities-overview.md)を使用して、コンテンツと技術的な改善点を特定します。
* データを書き出してチームでコラボレーションしたり、同僚を招待して製品を使用したりできます。

>[!NOTE]
> ブランド中心のエクスペリエンスで、左側のナビゲーションセクションから目的のビューにアクセスします。

最後に、LLM Optimizer の機能を完全に理解するには、使用できるすべての[ダッシュボード](/help/dashboards/dashboards-overview.md)を探索する必要があります。
