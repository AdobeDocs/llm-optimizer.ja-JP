---
title: Edge での最適化 - Cloudflare（BYOCDN）
description: LLM Optimizer の Edge での最適化に Cloudflare BYOCDN を設定する方法について説明します。
feature: Opportunities
autotag-review: '2026-07-15T17:46:02.378Z'
TQID: 'https://experienceleague.adobe.com/ZgOX0yC8qyb13Y7YNCg3Y1A6Q3TSk9-mUQ8gthzQvLM'
product_v2: id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2: id: d1956731-2adb-4bb7-8301-2b239254ac72id: e0828736-236a-487b-a478-5a635455eadcid: e1b649f0-0a61-46e4-9082-64d5cb2576c6id: ef4e63f5-cb4d-462d-bf9a-1f617edf2a3a
subfeature_v2: id: d23587d6-14d6-4e3f-9ee1-cc18623832e1id: e06fae5f-830b-4222-a469-b5e148d36465
role_v2: id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
topic_v2: id: c1579802-ddd4-4214-8a91-97b2066abe11id: d095671a-1355-40aa-8b5f-06c33c68080bid: eddd9b14-83bd-4ff4-9072-54a4a484abb7
source-git-commit: 2705cf26faea9c09817bbdcec4b4c531552df7ba
workflow-type: tm+mt
source-wordcount: 1919
ht-degree: 96%

---


# Cloudflare（BYOCDN）

この設定では、エージェントトラフィック（AI ボットおよび LLM ユーザーエージェントからのリクエスト）を Edge での最適化バックエンドサービス（`live.edgeoptimize.net`）にルーティングします。 人間の訪問者と SEO ボットは、通常どおりオリジンから引き続き提供されます。 設定をテストするには、設定が完了したら、応答のヘッダー `x-edgeoptimize-request-id` を探します。

**前提条件**

Cloudflare Workerのルーティングルールを設定する前に、次のことを確認してください。

* ドメインでワーカーが有効になっている Cloudflare アカウント。
* Cloudflare のドメイン DNS 設定へのアクセス権。
* LLM Optimizer UI から取得された Edge Optimize API キー。 手順について詳しくは、[API キーの取得](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#production-api-key)を参照してください。
* （オプション）ステージングルーティングをテストするには、[Staging API キー](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#staging-api-key-optional)を参照してください。

**ルーティングの仕組み**

正しく設定されると、エージェント型ユーザーエージェントからドメイン（例：`www.example.com/page.html`）へのリクエストは、Cloudflare ワーカーによって傍受され、Edge での最適化バックエンドにルーティングされます。 バックエンドリクエストには、必要なヘッダーが含まれます。

**バックエンドリクエストのテスト**

Edge での最適化バックエンドに直接リクエストを行うことで、ルーティングを検証できます。

```
curl -svo /dev/null https://live.edgeoptimize.net/page.html \
  -H 'x-forwarded-host: www.example.com' \
  -H 'x-edgeoptimize-url: /page.html' \
  -H 'x-edgeoptimize-api-key: $EDGE_OPTIMIZE_API_KEY' \
  -H 'x-edgeoptimize-config: LLMCLIENT=TRUE;'
```

**必須ヘッダー**

Edge での最適化バックエンドへのリクエストでは、次のヘッダーを設定する必要があります。

| ヘッダー | 説明 | 例 |
|--------|-------------|---------|
| `x-forwarded-host` | リクエストの元のホスト。 サイトドメインを特定するのに必要です。 | `www.example.com` |
| `x-edgeoptimize-url` | リクエストの元の URL パスとクエリ文字列。 | `/page.html` または `/products?id=123` |
| `x-edgeoptimize-api-key` | ドメイン用にアドビから提供された API キー。 | `your-api-key-here` |
| `x-edgeoptimize-config` | キャッシュキーを区別する設定文字列。 | `LLMCLIENT=TRUE;` |

## 設定オプション

Edge での最適化用の Cloudflare Worker を設定する方法には、次の 2 つがあります。

* [**オプション 1：Cloudflare にデプロイ（推奨）**](#option-1-deploy-to-cloudflare) - 新しいワーカーが自動的に作成され、必要な環境変数と秘密鍵の入力を求められます。 このドメインに既存の Cloudflare Worker がない場合は、このオプションを使用します。
* [**オプション 2：手動設定**](#option-2-manual-setup) - ワーカーを自分で作成および設定するための段階的な手順。 ドメインに既存の Cloudflare Worker が既に設定されている場合（既存のワーカーに Edge での最適化コードを結合する必要があります（[手順 2：Worker コードを追加](#option-2-manual-setup)を参照））またはデプロイメントを完全に制御する場合は、このオプションを使用します。

どちらのオプションを選択した場合でも、ワーカーをドメインに手動でリンクする必要があります。[手順：ドメインにルートを追加](#add-a-route-to-your-domain)を参照してください。

## オプション 1：Cloudflare にデプロイ

このオプションでは、「**Cloudflare にデプロイ**」ボタンを使用して、ワーカーを自動的に作成し、Cloudflare アカウントに必要な環境変数と秘密鍵を設定します。 新しいワーカーを設定する場合、これが最も簡単に開始できる方法です。

>[!IMPORTANT]
>
>このオプションは、ドメインに既存の Cloudflare Worker が&#x200B;**ない**&#x200B;場合にのみ使用します。 既にワーカーがある場合は、[オプション 2：手動設定](#option-2-manual-setup)を使用して、既存のワーカーに Edge での最適化のルーティングロジックを追加します。

**手順 1：ワーカーをデプロイ**

以下のボタンをクリックして、Edge での最適化ワーカーを Cloudflare アカウントにデプロイします。

[![Cloudflare にデプロイ](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/adobe/llmo-code-samples/tree/main/optimize-at-edge/cloudflare/automation)

**手順 2：デプロイメントフォームに入力**

ボタンをクリックすると、Worker の設定ページが開きます。 フォームを次のように入力します。

![Cloudflare Worker 設定ページ](/help/assets/optimize-at-edge/cloudflare-deploy-form.png)

1. **Git アカウント** - ドロップダウンメニューから GitHub または GitLab アカウントを選択します。 Cloudflare は、ワーカーコードをアカウントのリポジトリに分岐します。 アカウントがリストにない場合は、ドロップダウンメニューから「**+ 新規 GitHub 接続**」または「**+ 新規 GitLab 接続**」を選択して、新しい接続を直接追加できます。 詳しくは、[Cloudflare Git 統合ガイド](https://developers.cloudflare.com/workers/ci-cd/builds/git-integration/github-integration/)を参照してください。

   ![「新規 GitHub 接続」と「新規 GitLab 接続」のオプションを示す Git アカウントのドロップダウン](/help/assets/optimize-at-edge/cloudflare-git-connection.png)
2. **プライベート Git リポジトリを作成** - オンのままにしておきます（デフォルト）。
3. **プロジェクト名** - `edge-optimize-router` のままにしておくか、任意の名前を入力します。
4. **EDGE_OPTIMIZE_API_KEY** - アドビ提供の Edge Optimize API キーをペーストします。 この値は暗号化された秘密鍵として保存されます。
5. **EDGE_OPTIMIZE_TARGET_HOST** - プロトコルを除いたサイトのドメイン名を入力します（例：`www.example.com`）。
6. **ビルドコマンド** - 空のままにしておきます。
7. **デプロイコマンド** - `npm run deploy`（事前入力済み）のままにしておきます。
8. **本番以外の分岐のビルド** - オフのままにしておきます。 これは開発者のワークフロー機能で、このデプロイメントには必要ありません。
9. 「**作成してデプロイ**」をクリックします。

ワーカーがデプロイされたら、[ドメインにルートを追加](#add-a-route-to-your-domain)に進み、ワーカーをドメインにリンクさせます。 ルーティングは自動的に設定されないので、手動で設定する必要があります。

## オプション 2：手動設定

ワーカーを手動で作成および設定するには、次の手順に従います。.

**手順 1：Cloudflare ワーカーを作成**

1. Cloudflare ダッシュボードにログインします。
2. サイドバーの&#x200B;**ワーカーとページ**&#x200B;に移動します。
3. 「**アプリケーションを作成**」をクリックし、「**ワーカーを作成**」をクリックします。
4. ワーカーに名前を付けます（例：`edge-optimize-router`）。
5. 「**デプロイ**」をクリックし、デフォルトのコードでワーカーを作成します。

![Cloudflare ワーカーダッシュボード ](/help/assets/optimize-at-edge/cloudflare-workers-dashboard.png)

**手順 2：ワーカーコードを追加**

ワーカーを作成したら、**コードを編集**&#x200B;をクリックし、デフォルトコードを[worker.js](https://github.com/adobe/llmo-code-samples/blob/main/optimize-at-edge/cloudflare/automation/src/worker.js)のコードに置き換えます。 既存のCloudflare Workerがある場合は、コードを完全に置き換えるのではなく、既存のワーカーコードと結合します。

「**保存してデプロイ**」をクリックし、ワーカーを公開します。

**手順 3：環境変数と秘密鍵を設定**

環境変数には、API キーなどの機密性の高い設定が安全に保存されます。

1. ワーカーの設定で、**設定**／**変数**&#x200B;に移動します。
2. **環境変数**&#x200B;の下にある「**変数を追加**」をクリックします。
3. 次の変数を追加します。

   | 変数名 | 説明 | 必須 |
   |---------------|-------------|----------|
   | `EDGE_OPTIMIZE_API_KEY` | アドビ提供の Edge Optimize API キー。 | はい |
   | `EDGE_OPTIMIZE_TARGET_HOST` | Edge での最適化リクエストのターゲットホスト（`x-forwarded-host` ヘッダーとして送信）とフェイルオーバーのオリジンドメイン。 プロトコルなしのドメインのみを指定する必要があります（例：`https://www.example.com` ではなく `www.example.com`）。 | はい |

4. API キーの場合は、「**暗号化**」をクリックし、安全に保存します。
5. 「**保存してデプロイ**」をクリックします。

![Cloudflare 環境変数](/help/assets/optimize-at-edge/cloudflare-env-variables.png)

## ドメインにルートを追加 {#add-a-route-to-your-domain}

どの設定オプションを使用した場合でも、ワーカーをドメインに手動でリンクする必要があります。 この手順で、トラフィック上でワーカーがアクティブ化されます。

1. ワーカーの&#x200B;**設定**／**トリガー**&#x200B;に移動します。
2. **ルート**&#x200B;の下にある「**ルートを追加**」をクリックします。
3. ドメインパターンを入力します（例：`www.example.com/*` または `example.com/*`）。
4. ドロップダウンからゾーンを選択します。
5. 「**保存**」をクリックします。

または、ゾーンレベルでルートを設定することもできます。

1. Cloudflare のドメインに移動します。
2. **ワーカールート**&#x200B;に移動します。
3. 「**ルートを追加**」をクリックし、パターンとワーカーを指定します。

![Cloudflare ワーカールート](/help/assets/optimize-at-edge/cloudflare-worker-routes.png)

**フェイルオーバー動作の検証**

Edge での最適化が使用できない場合やエラーが返された場合、ワーカーは自動的にオリジンにフェイルオーバーします。 フェイルオーバー応答には、`x-edgeoptimize-fo` ヘッダーが含まれます。

```
< HTTP/2 200
< x-edgeoptimize-fo: 1
```

Cloudflare ワーカーのログでフェイルオーバーイベントを監視して、問題のトラブルシューティングを行うことができます。

**ワーカーロジックについて**

Cloudflare ワーカーは、次のロジックを実装します。

1. **ユーザーエージェント検出：**&#x200B;受信リクエストのユーザーエージェントが、定義済みのエージェント型ボットのいずれかと一致するかどうかを確認します（大文字と小文字を区別しません）。

2. **パスターゲティング：**&#x200B;オプションで、ターゲットパスに基づいてリクエストをフィルタリングします。 デフォルトでは、すべての HTML ページ（末尾が `/`、拡張子なし、または `.html` の URL）がルーティングされます。 `TARGETED_PATHS` 配列を使用して、特定のパスを指定できます。

3. **ループ保護：**`x-edgeoptimize-request` ヘッダーは無限ループを防ぎます。 Edge での最適化がオリジンにリクエストを戻す際、このヘッダーは `"1"` に設定され、ワーカーはリクエストを Edge での最適化にルーティングせずに渡します。

4. **ヘッダーセキュリティ：** Edge での最適化ヘッダーを設定する前に、ワーカーは受信リクエストから既存の `x-edgeoptimize-*` ヘッダーを削除し、ヘッダーインジェクション攻撃を防ぎます。

5. **ヘッダーマッピング：**&#x200B;ワーカーは Edge での最適化に必要なヘッダーを設定します。
   * `x-forwarded-host` - 元のサイトドメインを特定します。
   * `x-edgeoptimize-url` - 元のリクエストパスとクエリ文字列を保持します。
   * `x-edgeoptimize-api-key` - Edge での最適化でリクエストを認証します。
   * `x-edgeoptimize-config` - キャッシュキーの設定を指定します。

6. **フェイルオーバーロジック**：Edge での最適化がエラーステータスコード（4XX クライアントエラーまたは 5XX サーバーエラー）を返した場合や、ネットワークエラーによりリクエストが失敗した場合、ワーカーは `EDGE_OPTIMIZE_TARGET_HOST` を使用してオリジンに自動的にフェイルオーバーします。 フェイルオーバー応答には、フェイルオーバーが発生したことを示す `x-edgeoptimize-fo: 1` ヘッダーが含まれます。

7. **リダイレクト処理：**`redirect: "manual"` オプションを指定すると、Edge での最適化からのリダイレクト応答がワーカーを経由せずにクライアントに渡されます。

**設定のカスタマイズ**

コードの上部にある設定定数を変更して、ワーカーの動作をカスタマイズできます。

**エージェント型ボットリスト**

`AGENTIC_BOTS` 配列を変更して、ユーザーエージェントを追加または削除します。

```javascript
const AGENTIC_BOTS = [
  'AdobeEdgeOptimize-AI',
  'ChatGPT-User',
  'GPTBot',
  'OAI-SearchBot',
  'PerplexityBot',
  'Perplexity-User',
  'ClaudeBot',
  'Claude-User',
  'Claude-SearchBot',
  // Add additional user agents as needed
];
```

**ターゲットパス**

デフォルトでは、すべての HTML ページが Edge での最適化にルーティングされます。 特定のパスにルーティングを制限するには、`TARGETED_PATHS` 配列を変更します。

```javascript
// Route all HTML pages (default)
const TARGETED_PATHS = null;

// Or specify exact paths to route
const TARGETED_PATHS = ['/', '/page.html', '/products', '/about-us'];
```

**フェイルオーバー設定**

デフォルトでは、Edge での最適化から 4XX または 5XX エラーが発生した場合、ワーカーはフェイルオーバーします。 この動作をカスタマイズします。

```javascript
// Default: failover on any 4XX or 5XX error
const FAILOVER_ON_4XX = true;
const FAILOVER_ON_5XX = true;

// Failover only on 5XX server errors (not 4XX client errors)
const FAILOVER_ON_4XX = false;
const FAILOVER_ON_5XX = true;

// Disable automatic failover (not recommended)
const FAILOVER_ON_4XX = false;
const FAILOVER_ON_5XX = false;
```

**重要な検討事項**

* **フェイルオーバー動作：** Edge での最適化がエラー（4XX または 5XX ステータスコード）を返した場合や、ネットワークエラーによりリクエストが失敗した場合、ワーカーは自動的にオリジンサーバーにフェイルオーバーします。 フェイルオーバーでは、オリジンドメインとして `EDGE_OPTIMIZE_TARGET_HOST` が使用されます（Fastly の `F_Default_Origin` やCloudFront の `Default_Origin` と同様）。 フェイルオーバー応答には、監視やデバッグに使用できる `x-edgeoptimize-fo: 1` ヘッダーが含まれます。

* **キャッシュ：** Cloudflare はデフォルトで URL に基づいて応答をキャッシュします。 エージェントトラフィックは人間のトラフィックとは異なるコンテンツを受信するので、キャッシュ設定でこの点を考慮してください。 キャッシュされたコンテンツを区別するには、Cache API またはキャッシュヘッダーの使用を検討します。 `x-edgeoptimize-config` ヘッダーはキャッシュキーに含める必要があります。

* **レート制限**：Edge での最適化の使用状況を監視し、必要に応じてエージェントトラフィックのレート制限を実装することを検討してください。

* **テスト：**&#x200B;本番環境にデプロイする前に、常にステージング環境で設定をテストします。 エージェントトラフィックと人間のトラフィックの両方が期待どおりに動作することを検証します。 Edge での最適化のエラーをシミュレートして、フェイルオーバー動作をテストします。

* **ログ：** Cloudflare ワーカーのログを有効にして、リクエストの監視や問題のトラブルシューティングを行います。 リアルタイムログを表示するには、**ワーカー**／**お客様のワーカー**／**ログ**&#x200B;に移動します。 ワーカーは、デバッグの目的でフェイルオーバーイベントをログに記録します。

**トラブルシューティング**

| 問題 | 考えられる原因 | 解決策 |
|-------|----------------|----------|
| 応答に `x-edgeoptimize-request-id` ヘッダーがない | ワーカールートが一致していないか、エージェントボットリストにユーザーエージェントがありません。 | ルートパターンがリクエスト URL と一致することを検証します。 ユーザーエージェントが `AGENTIC_BOTS` 配列にあることを確認します。 |
| Edge での最適化からの 401 または 403 エラー | API キーが無効または欠落しています。 | 環境変数と秘密鍵に `EDGE_OPTIMIZE_API_KEY` が正しく設定されていることを検証します。 アドビに問い合わせて、API キーがアクティブであることを確認します。 |
| 無限リダイレクトまたはループ | ループ保護ヘッダーが正しく設定または確認されていません。 | `x-edgeoptimize-request` ヘッダーの確認が行われていることを確認します。 |
| 人間のトラフィックが影響を受けている | ワーカールーティングロジックが広すぎます。 | ユーザーエージェントの一致ロジックが正しく、大文字と小文字が区別されないことを検証します。 `TARGETED_PATHS` が正しく設定されていることを確認します。 |
| 応答時間が遅い | Edge での最適化バックエンドに対してネットワーク遅延が発生しています。 | これは最初のリクエストで想定されます。後続のリクエストは、Edge での最適化でキャッシュされます。 |
| 応答の `x-edgeoptimize-fo: 1` ヘッダー | Edge での最適化がエラーを返し、オリジンへのフェールオーバーが発生しました。 | Cloudflare ワーカーのログで特定のエラーコードを確認します。 アドビで Edge での最適化サービスのステータスを検証します。 |
| フェイルオーバーが機能しない | フェイルオーバーフラグが無効になっているか、フェイルオーバーロジックでエラーが発生しています。 | `FAILOVER_ON_4XX` と `FAILOVER_ON_5XX` が `true` に設定されていることを検証します。 ワーカーログにエラーメッセージがないかを確認します。 |
| 特定のパスが最適化されていない | パスがターゲットパスまたは HTML ページパターンと一致していません。 | パスが `TARGETED_PATHS`（指定されている場合）にあり、HTML ページの正規表現パターンと一致していることを検証します。 |
| リクエストが無効なホストで失敗する | `EDGE_OPTIMIZE_TARGET_HOST` にプロトコル（例：`https://`）が含まれています。 | プロトコルなしのドメイン名のみを使用します（例：`https://example.com` ではなく `example.com`）。 |
| フェイルオーバー中の 530 エラー | Cloudflare がオリジンに接続できないか、フェイルオーバーリクエストに無効なヘッダーがあります。 | フェイルオーバー機能で Edge での最適化ヘッダーが削除されていることを確認します。 オリジンがアクセス可能で、DNS が正しく設定されていることを検証します。 |

**ファイアウォールルールを通じて Edge での最適化を許可（オプション）**

{{waf-allowlist-setup}}

**設定の検証**

設定が完了したら、ボットトラフィックが Edge での最適化にルーティングされていることと、人間のトラフィックに影響がないことを確認します。

**1. ボットトラフィックをテスト（最適化する必要があります）**

エージェント型ユーザーエージェントを使用して、AI ボットリクエストをシミュレートします。

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: chatgpt-user"
```

正常な応答には、リクエストが Edge での最適化を経由してルーティングされたことを確認する `x-edgeoptimize-request-id` ヘッダーが含まれます。

```
< HTTP/2 200
< x-edgeoptimize-request-id: 50fce12d-0519-4fc6-af78-d928785c1b85
```

**2. 人間のトラフィックをテスト（影響を受けません）**

通常の人間によるブラウザーリクエストをシミュレートします。

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36"
```

応答には、`x-edgeoptimize-request-id` ヘッダーを含め&#x200B;**ない**&#x200B;でください。 ページのコンテンツと応答時間は、Edge での最適化を有効にする前と同じ状態を維持する必要があります。

**3. 2 つのシナリオを区別する方法**

| ヘッダー | ボットトラフィック（最適化） | 人間のトラフィック（影響を受けない） |
|---|---|---|
| `x-edgeoptimize-request-id` | 存在 - 一意のリクエスト ID が含まれます | 不在 |
| `x-edgeoptimize-fo` | フェイルオーバーが発生した場合のみ存在（値：`1`） | 不在 |

{{verify-routing-status-in-ui}}

{{return-to-overview}}
