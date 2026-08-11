---
title: クイックスタート
description: ブランド名とドメインをオンボードする方法、Experience Hub または Experience Cloud から体験版をアクティブ化する方法、Adobe LLM Optimizer の設定を完了する方法について説明します。
feature: Quickstart, Onboarding
autotag-review: '2026-07-15T18:07:16.514Z'
TQID: 'https://experienceleague.adobe.com/Hp5j1st4fkfiBVKTTL-eHQX6Ovmw61-2hX2g1T8Ui8Y'
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: a080bb92-ba2a-4e53-ba60-f5184d1a9e9aid: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2: id: d23587d6-14d6-4e3f-9ee1-cc18623832e1
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bdid: b69b2659-1057-424e-8fc5-ed9e016dc554
topic_v2: id: cdd65e7e-8839-44a2-bc21-0e03623b5dd1id: d00e9f03-e50b-4162-b143-0c0817c937c2id: e1e0219c-f879-479f-8427-888ed2a6e9c2
source-git-commit: 2705cf26faea9c09817bbdcec4b4c531552df7ba
workflow-type: ht
source-wordcount: 1201
ht-degree: 100%

---


# クイックスタート

LLM Optimizer を開始するには、オンボーディングプロセスを完了します。 次に、カテゴリ、トピック、プロンプトをカスタマイズし、CDN ログ転送を設定し、完全なインサイトを得るために[ダッシュボード](/help/dashboards/dashboards-overview.md)を開きます。

<!--Where steps differ by layout, use **Customer Configuration (classic experience)** or **Brands Management** / **Prompts Management**, whichever matches your current interface.-->

## ブランド中心のエクスペリエンス {#brand-centric-experience}

デフォルトでは、顧客はオンボーディング駆動型の設定により、焦点を当てたブランドファーストのインターフェイスで開始します。 このインターフェイスでは、各組織は 1 つのアクティブなブランドから開始し、追加の推奨ブランドから選択できます。<!--Existing LLM Optimizer customers will shift to this Brand Centric experience gradually.-->

## オンボーディングの概要

オンボーディングプロセスは、ドメインとブランド名のオンボーディングから開始します。 オンボーディングジャーニーの各パートについて詳しくは、LLM Optimizer の使用をできるだけ早く開始する方法に関する役立つヒントと共に以下を参照してください。

### Adobe LLM Optimizer で公開ページにアクセスできるようにする

正確なコンテンツと技術的なレコメンデーションを提供するには、Adobe LLM Optimizer で公開ページにアクセスする必要があります。 これは、安全な内部 web クローラー（Spacecat/1.0 ユーザーエージェント）を通じて実現されます。

設定要件：

* Spacecat/1.0 ユーザーエージェントをサイトの robots.txt ファイルまたはボットトラフィック管理ルールの許可リストに追加します。
* ページがドメインレベルや CDN レベルでブロックされていないことを確認します。 ブロックされたページでは、インデックスを作成できません。つまり、これらのページに対して最適化タスクとインサイトを生成できません。

ダッシュボードでコンテンツの可視性が低いと表示される場合は、web クローラーがドメインにアクセスできることを確認してください。 制限付きアクセスは、インデックス作成が不完全になる一般的な原因です。

## 手順 1：ブランド名とドメインをオンボード {#step-1-onboard-your-domain}

LLM Optimizer を開始するには、最初に（適格な場合）体験版をアクティブ化し、ブランド名とドメインをオンボードします。

### 体験版のアクティブ化

アクティブ化フローは、お使いのアドビ製品によって異なります。

#### AEM Cloud のお客様

AEM Cloud のお客様は、体験版をアクティブ化するために、次のいずれかを実行できます。

* [Experience Hub](https://experienceleague.adobe.com/ja/docs/experience-manager-cloud-service/content/experience-hub/experience-hub) に移動し、お知らせカードを使用して LLM Optimizer をアクティブ化します。 「**LLM Optimizerを試す**」を選択すると、[https://llmo.now](https://llmo.now) にリダイレクトされます。 IMS を介してログインし、ドメインとブランド名を入力してオンボーディングプロセスを開始します。
* または、[https://llmo.now](https://llmo.now) に直接移動してログインします。

![LLM Optimizer 体験版](/help/overview/assets/llm-trial.png)

#### Adobe Analytics と Adobe Customer Journey Analytics

Adobe Analytics と Adobe Customer Journey Analytics のお客様の場合は、Experience Cloud のホームページにバナーが表示されます。

![Experience Cloud ホームページと「Adobe LLM Optimizer体験版を開始」バナー](/help/overview/assets/experience-cloud-llmo-trial-banner.png)

体験版は、次のいずれかの方法でアクティブ化できます。

* バナーで「**Adobe LLM Optimizer 体験版を開始**」を選択します。
* [https://llmo.now](https://llmo.now) に直接移動してログインします。

体験版がアクティブになったら、ブランド名とドメインのオンボーディングを続行します。

>[!NOTE]
>
> * **無料体験版**：AEM Cloud および Adobe Analytics／Customer Journey Analytics のお客様は、LLM Optimizer の無料体験版をご利用いただけます。
> * **2026年4月1日（PT）以降に体験版をアクティブ化したお客様**&#x200B;は、最大 100 個のプロンプト、1 個のドメインを使用でき、単一のオポチュニティタイプに対して最大 10 個の URL をまたいで最適化をデプロイできます。
> * **2026年4月1日（PT）より前に体験版をアクティブ化したお客様**&#x200B;は、既存の条件に従って、引き続き最大 200 個のプロンプトへのアクセス権が付与されます。
>
>含まれる制限を超えて使用するには、別途ライセンス契約が必要です。 アクセス権は「現状有姿」および「利用可能な範囲」で付与され、いつでも変更、制限、削除される場合があります。 詳しくは、アカウント担当者にお問い合わせください。

#### ブランド名とドメインのオンボーディング

LLM Optimizer の使用を開始するには、ブランド名とドメインをオンボードします。

1. ブランド名と関連ドメインを入力します。

   * これは、コンテンツの分析と最適化を行うメインドメインです。

1. オンボーディングを完了します。

   * 送信が完了すると、LLM Optimizer はドメインの分析とインサイトの生成を開始します。

![LLM Optimizer ドメイン](/help/overview/assets/domain.png)

>[!NOTE]
>新しく追加されたプロンプトは、処理が完了するまで[ブランドプレゼンスダッシュボード](/help/dashboards/brand-presence.md)に表示されません。

>[!NOTE]
>指定したドメインは、組織内のすべてのユーザーによって使用され、変更できません。

オンボーディングフェーズ中に、カテゴリ、トピック、プロンプトの小さなセットが生成されます。 これらのプロンプトでのブランドプレゼンス分析は、サイトをオンボードした直後に使用できるようになります。

また、Edge での最適化をデプロイする機能も使用できます。 詳しくは、[Edge での最適化 - よくある質問](https://experienceleague.adobe.com/ja/docs/llm-optimizer/using/resources/optimize-at-edge/overview#frequently-asked-questions)を参照してください。

さらに、トラフィック分析用に [CDN ログ転送](#step-4)を設定します。 LLM Optimizer では、機会を特定し、AI の可視性を向上させる規範的なレコメンデーションを提供するには、ブランドプレゼンスデータと、エージェントトラフィックおよびリファラルトラフィックからのインサイトが必要です。

### AEM Cloud 以外のお客様

組織がビジネス契約を完了したら、組織が選択したドメインで LLM Optimizer にオンボードされます。 オンボーディングが完了したら、[https://llmo.now](https://llmo.now) でログインします。

## 手順 2：カテゴリ、トピック、プロンプトをカスタマイズ {#step-2-customize-categories-topics-and-prompts}

サイトにオンボードしたら、オンボーディングフェーズ中に自動生成された小さなセットのプロンプトに基づいて、ブランドプレゼンス分析を表示できます。 今後は、ブランドのカテゴリ、トピック、プロンプトをカスタマイズできます。

### ブランド中心のエクスペリエンスのカテゴリ、トピック、プロンプト

カテゴリ、トピック、プロンプトは、次のように追加できます。

* **カテゴリ** - **ブランド管理**&#x200B;に移動し、「**カテゴリ**」をクリックします。 カテゴリはグローバルレベルで定義され、ブランド管理の下にあるすべてのブランドに適用されます。

  ![ナビゲーションにカテゴリを含むブランド管理](/help/assets/brand-centric-experience/llmo-app-shell.png)

* **トピックとプロンプト** - **プロンプト管理**&#x200B;に移動して、特定のブランドのプロンプトを含む、トピックとプロンプトを作成します。

  ![プロンプト管理](/help/assets/brand-centric-experience/prompts-management.png)

>[!NOTE]
>LLM に尋ねる正確なプロンプトは、LLM によって開示されていないので、公開されていません。

>[!NOTE]
>
> カテゴリ、トピック、プロンプトを設定する方法について詳しくは、[カテゴリ、トピック、プロンプトの設定のベストプラクティス](/help/overview/best-practices-topics-prompts.md)ページを参照してください。

## 手順 3：ブランドプレゼンスインサイト

ドメインをオンボードした後、オンボーディング中に自動的に生成されたプロンプトに基づいて、ブランドプレゼンスビューに初期インサイトが表示されます。 独自のカテゴリ、トピック、プロンプトをカスタマイズすると、LLM Optimizer では指定したプロンプトに基づいてブランドプレゼンス分析を自動的にトリガーし、結果は 24 時間以内に使用できるようになります。

**ブランドプレゼンス**&#x200B;に移動し、ブランドドロップダウンを使用して、ブランドプレゼンスを表示するブランドを選択します。 また、このエクスペリエンスでは、**すべてのブランド**&#x200B;レベルでブランドの可視性を確認することもできます。

## 手順 4：CDN ログ転送の情報を指定 {#step-4}

エージェントトラフィックとリファラルトラフィックに関するインサイトを取得するには、CDN ログ転送を登録して、LLM Optimizer がアクセスログを読み取れるようにします。

### CDN ログ転送

**ブランド管理**&#x200B;から次のように CDN ログ転送情報を追加できます。**ブランド管理**&#x200B;を開き、**CDN** ラベルをクリックします。

![ブランド管理 - CDN ログ転送](/help/assets/brand-centric-experience/brands-management-cdn.png)

>[!NOTE]
>顧客管理 CDN（BYOCDN）を使用する際のログ転送について詳しくは、[BYOCDN ログ転送の概要](/help/overview/log-forwarding/log-forwarding-overview.md)を参照してください。


## 手順 5：ダッシュボードを探索し、アクションを実行

CDN ログ転送に関する情報を入力すると、左側のナビゲーションセクションから目的のダッシュボードにアクセスできます。

* [ブランドプレゼンス](/help/dashboards/brand-presence.md)ダッシュボードを表示して、可視性スコアを確認し、その他のブランドと比較したパフォーマンスを追跡します。
* CDN ログ転送が設定されている場合は、[エージェントトラフィック](/help/dashboards/agentic-traffic.md)ダッシュボードと[リファラルトラフィック](/help/dashboards/referral-traffic.md)ダッシュボードを探索します。
* [オポチュニティ](/help/dashboards/opportunities-overview.md)を使用して、コンテンツと技術的な改善点を特定します。
* データを書き出してチームでコラボレーションしたり、同僚を招待して製品を使用したりできます。

最後に、LLM Optimizer の機能を完全に理解するには、使用できるすべての[ダッシュボード](/help/dashboards/dashboards-overview.md)を探索する必要があります。
