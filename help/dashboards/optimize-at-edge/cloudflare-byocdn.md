---
title: Edge での最適化 - Cloudflare（BYOCDN）
description: LLM Optimizer の Edge での最適化に Cloudflare BYOCDN を設定する方法について説明します。
feature: Opportunities
source-git-commit: 13d2f4bbd1f9d3886f89f80df0e76093f2afdf13
workflow-type: tm+mt
source-wordcount: '1906'
ht-degree: 68%

---


# Cloudflare（BYOCDN）

この設定では、エージェントトラフィック（AI ボットおよび LLM ユーザーエージェントからのリクエスト）を Edge での最適化バックエンドサービス（`live.edgeoptimize.net`）にルーティングします。 人間の訪問者と SEO ボットは、通常どおりオリジンから引き続き提供されます。 設定をテストするには、設定が完了したら、応答のヘッダー `x-edgeoptimize-request-id` を探します。

**前提条件**

Cloudflare ワーカールーティングルールを設定する前に、以下を確認します。

* ドメインでワーカーが有効になっている Cloudflare アカウント。
* Cloudflare のドメイン DNS 設定へのアクセス権。
* LLM Optimizer UI から取得された Edge Optimize API キー。 手順については、[API キーの取得](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#production-api-key)を参照してください。
* （オプション）ステージング ルーティングをテストするには、[&#x200B; ステージング API キー](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#staging-api-key-optional)を参照してください。

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

Edge Optimize用にCloudflare Workerを設定するには、次の2つの方法があります。

* [**オプション 1: Cloudflareへのデプロイ （推奨）**](#option-1-deploy-to-cloudflare) – 新しいワーカーを自動的に作成し、必要な環境変数とシークレットの入力を求めるメッセージを表示します。 このドメインに既存のCloudflare Workerがない場合は、このオプションを使用します。
* [**オプション 2：手動セットアップ**](#option-2-manual-setup) — ワーカーを自分で作成および設定するための手順ごとの手順。 ドメインに既存のCloudflare Workerが既に設定されている場合は、このオプションを使用します。Edge Optimize コードを既存のワーカーにマージする必要があります（[&#x200B; ステップ 2: ワーカーコードを追加](#option-2-manual-setup)を参照）。また、デプロイメントを完全に制御する必要がある場合は、このオプションを使用します。

選択したオプションに関係なく、ワーカーをドメインに手動でリンクする必要があります。[手順：ドメインへのルートの追加](#add-a-route-to-your-domain)を参照してください。

## オプション 1:Cloudflareへのデプロイ

このオプションでは、「**Cloudflareにデプロイ**」ボタンを使用して、ワーカーを自動的に作成し、Cloudflare アカウントに必要な環境変数とシークレットを設定します。 これは、新しいワーカーを設定する場合に最も簡単に開始できる方法です。

>[!IMPORTANT]
>
>このオプションは、**がドメインに既存のCloudflare Workerを持っていない**&#x200B;場合にのみ使用してください。 既にワーカーがある場合は、[&#x200B; オプション 2：手動セットアップ &#x200B;](#option-2-manual-setup)を使用して、既存のワーカーにEdge Optimize ルーティングロジックを追加します。

**手順1: ワーカーのデプロイ**

以下のボタンをクリックして、Edge Optimize ワーカーをCloudflare アカウントにデプロイします。

[![Cloudflareへのデプロイ &#x200B;](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/adobe/llmo-code-samples/tree/main/optimize-at-edge/cloudflare/automation)

**手順2：展開フォームに入力**

ボタンをクリックすると、ワーカーの設定ページが開きます。 次のようにフォームに入力します。

![Cloudflare Workers設定ページ &#x200B;](/help/assets/optimize-at-edge/cloudflare-deploy-form.png)

1. **Git アカウント** — ドロップダウンからGitHubまたはGitLab アカウントを選択します。 Cloudflareは、ワーカーコードをアカウントのリポジトリにフォークします。 アカウントがリストにない場合は、**+新規GitHub Connection**&#x200B;または&#x200B;**+新規GitLab Connection**&#x200B;を選択して、ドロップダウンから直接新しい接続を追加できます。 詳しくは、[Cloudflare Git統合ガイド &#x200B;](https://developers.cloudflare.com/workers/ci-cd/builds/git-integration/github-integration/)を参照してください。

   新しいGitHub接続と新しいGitLab接続オプションを表示する![Git アカウントのドロップダウン &#x200B;](/help/assets/optimize-at-edge/cloudflare-git-connection.png)
2. **プライベート Git リポジトリを作成** – このチェックをオンのままにします（デフォルト）。
3. **プロジェクト名** — `edge-optimize-router`のままにするか、任意の名前を入力してください。
4. **EDGE_OPTIMIZE_API_KEY** — Adobeが提供するEdge Optimize API キーを貼り付けます。 この値は、暗号化された秘密鍵として保存されます。
5. **EDGE_OPTIMIZE_TARGET_HOST** — プロトコルなしでサイトのドメインを入力します（例：`www.example.com`）。
6. **ビルド コマンド** – 空のままにします。
7. **デプロイ コマンド** — `npm run deploy`のままにします（事前入力）。
8. **実稼動以外のブランチ用のビルド** – 選択を解除します。 これは開発者ワークフロー機能であり、このデプロイメントには必要ありません。
9. 「**作成してデプロイ**」をクリックします。

ワーカーをデプロイしたら、[&#x200B; ドメインへのルートの追加](#add-a-route-to-your-domain)に進み、ワーカーをドメインにリンクします。 ルーティングは自動的に設定されないので、手動で完了する必要があります。

## オプション 2：手動セットアップ

ワーカーを手動で作成および設定するには、次の手順に従います。

**手順 1：Cloudflare ワーカーを作成**

1. Cloudflare ダッシュボードにログインします。
2. サイドバーの&#x200B;**ワーカーとページ**&#x200B;に移動します。
3. 「**アプリケーションを作成**」をクリックし、「**ワーカーを作成**」をクリックします。
4. ワーカーに名前を付けます（例：`edge-optimize-router`）。
5. 「**デプロイ**」をクリックし、デフォルトのコードでワーカーを作成します。

![Cloudflare ワーカーダッシュボード &#x200B;](/help/assets/optimize-at-edge/cloudflare-workers-dashboard.png)

**手順 2：ワーカーコードを追加**

ワーカーを作成したら、**コードを編集**&#x200B;をクリックし、デフォルトコードを次のコードに置き換えます。 既存のCloudflare Workerがある場合は、以下のコードを完全に置き換えるのではなく、既存のワーカーコードとマージします。

```javascript
/**
 * Edge Optimize BYOCDN - Cloudflare Worker
 *
 * This worker routes requests from agentic bots (AI/LLM user agents) to the
 * Edge Optimize backend service for optimized content delivery.
 *
 * Features:
 * - Routes agentic bot traffic to Edge Optimize backend
 * - Failover to origin on Edge Optimize errors (any 4XX or 5XX errors)
 * - Loop protection to prevent infinite redirects
 * - Human visitors and SEO bots are served from the origin as usual
 */

// List of agentic bot user agents to route to Edge Optimize
const AGENTIC_BOTS = [
  'AdobeEdgeOptimize-AI',
  'ChatGPT-User',
  'GPTBot',
  'OAI-SearchBot',
  'PerplexityBot',
  'Perplexity-User'
];

// Targeted paths for Edge Optimize routing
// Set to null to route all HTML pages, or specify an array of paths
const TARGETED_PATHS = null; // e.g., ['/', '/page.html', '/products']

// Failover configuration
// Failover on any 4XX client error or 5XX server error from Edge Optimize
const FAILOVER_ON_4XX = true; // Failover on any 4XX error (400-499)
const FAILOVER_ON_5XX = true; // Failover on any 5XX error (500-599)

export default {
  async fetch(request, env, ctx) {
    return await handleRequest(request, env, ctx);
  },
};

async function handleRequest(request, env, ctx) {
  const url = new URL(request.url);
  const userAgent = request.headers.get("user-agent")?.toLowerCase() || "";

  // Check if request is already processed (loop protection)
  const isEdgeOptimizeRequest = !!request.headers.get("x-edgeoptimize-request");

  // Construct the original path and query string
  const pathAndQuery = `${url.pathname}${url.search}`;

  // Check if the path matches HTML pages (no extension or .html extension)
  const isHtmlPage = /(?:\/[^./]+|\.html|\/)$/.test(url.pathname);

  // Check if path is in targeted paths (if specified)
  const isTargetedPath = TARGETED_PATHS === null
    ? isHtmlPage
    : (isHtmlPage && TARGETED_PATHS.includes(url.pathname));

  // Check if user agent is an agentic bot
  const isAgenticBot = AGENTIC_BOTS.some((ua) => userAgent.includes(ua.toLowerCase()));

  // Route to Edge Optimize if:
  // 1. Request is NOT already from Edge Optimize (prevents infinite loops)
  // 2. User agent matches one of the agentic bots
  // 3. Path is targeted for optimization
  if (!isEdgeOptimizeRequest && isAgenticBot && isTargetedPath) {

    // Build the Edge Optimize request URL
    const edgeOptimizeURL = `https://live.edgeoptimize.net${pathAndQuery}`;

    // Clone and modify headers for the Edge Optimize request
    const edgeOptimizeHeaders = new Headers(request.headers);

    // Remove any existing Edge Optimize headers for security
    edgeOptimizeHeaders.delete("x-edgeoptimize-api-key");
    edgeOptimizeHeaders.delete("x-edgeoptimize-url");
    edgeOptimizeHeaders.delete("x-edgeoptimize-config");
    edgeOptimizeHeaders.delete("x-edgeoptimize-fetcher-key"); // Optional (required only in case of WAF)

    // x-forwarded-host: The original site domain
    // Use environment variable if set, otherwise use the request host
    edgeOptimizeHeaders.set("x-forwarded-host", env.EDGE_OPTIMIZE_TARGET_HOST ?? url.host);

    // x-edgeoptimize-api-key: Your Adobe-provided API key
    edgeOptimizeHeaders.set("x-edgeoptimize-api-key", env.EDGE_OPTIMIZE_API_KEY);

    // x-edgeoptimize-url: The original request URL path and query
    edgeOptimizeHeaders.set("x-edgeoptimize-url", pathAndQuery);

    // x-edgeoptimize-config: Configuration for cache key differentiation
    edgeOptimizeHeaders.set("x-edgeoptimize-config", "LLMCLIENT=TRUE;");

    // edgeOptimizeHeaders.set("x-edgeoptimize-fetcher-key", "<YOUR FETCHER KEY>"); // Optional (required only in case of WAF)

    try {
      // Send request to Edge Optimize backend
      const edgeOptimizeResponse = await fetch(new Request(edgeOptimizeURL, {
        headers: edgeOptimizeHeaders,
        redirect: "manual", // Preserve redirect responses from Edge Optimize
      }), {
        cf: {
          cacheEverything: true, // Enable caching based on origin's cache-control headers
        },
      });

      // Check if we need to failover to origin
      const status = edgeOptimizeResponse.status;
      const is4xxError = FAILOVER_ON_4XX && status >= 400 && status < 500;
      const is5xxError = FAILOVER_ON_5XX && status >= 500 && status < 600;

      if (is4xxError || is5xxError) {
        console.log(`Edge Optimize returned ${status}, failing over to origin`);
        return await failoverToOrigin(request, env, url);
      }

      // Return the Edge Optimize response
      return edgeOptimizeResponse;

    } catch (error) {
      // Network error or timeout - failover to origin
      console.log(`Edge Optimize request failed: ${error.message}, failing over to origin`);
      return await failoverToOrigin(request, env, url);
    }
  }

  // For all other requests (human users, SEO bots), pass through unchanged
  return fetch(request);
}

/**
 * Failover to origin server when Edge Optimize returns an error
 * @param {Request} request - The original request
 * @param {Object} env - Environment variables
 * @param {URL} url - Parsed URL object
 */
async function failoverToOrigin(request, env, url) {
  // Build origin URL
  const originURL = `${url.protocol}//${env.EDGE_OPTIMIZE_TARGET_HOST}${url.pathname}${url.search}`;

  // Prepare headers - clean Edge Optimize headers and add loop protection
  const originHeaders = new Headers(request.headers);
  originHeaders.set("Host", env.EDGE_OPTIMIZE_TARGET_HOST);
  originHeaders.delete("x-edgeoptimize-api-key");
  originHeaders.delete("x-edgeoptimize-url");
  originHeaders.delete("x-edgeoptimize-config");
  originHeaders.delete("x-forwarded-host");
  originHeaders.set("x-edgeoptimize-request", "fo");

  // Create and send origin request
  const originRequest = new Request(originURL, {
    method: request.method,
    headers: originHeaders,
    body: request.body,
    redirect: "manual",
  });

  const originResponse = await fetch(originRequest);

  // Add failover marker header to response
  const modifiedResponse = new Response(originResponse.body, {
    status: originResponse.status,
    statusText: originResponse.statusText,
    headers: originResponse.headers,
  });
  modifiedResponse.headers.set("x-edgeoptimize-fo", "1");
  return modifiedResponse;
}
```

「**保存してデプロイ**」をクリックし、ワーカーを公開します。

![Cloudflare ワーカーコードエディター](/help/assets/optimize-at-edge/cloudflare-worker-editor.png)

**手順3：環境変数とシークレットの設定**

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

## ドメインへのルートの追加 {#add-a-route-to-your-domain}

使用した設定オプションに関係なく、ワーカーをドメインに手動でリンクする必要があります。 このステップは、トラフィック上のワーカーをアクティブにします。

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
  // Add additional user agents as needed
  'ClaudeBot',
  'Anthropic-AI'
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
| Edge での最適化からの 401 または 403 エラー | API キーが無効または欠落しています。 | `EDGE_OPTIMIZE_API_KEY`が環境変数とシークレットで正しく設定されていることを確認します。 アドビに問い合わせて、API キーがアクティブであることを確認します。 |
| 無限リダイレクトまたはループ | ループ保護ヘッダーが正しく設定または確認されていません。 | `x-edgeoptimize-request` ヘッダーの確認が行われていることを確認します。 |
| 人間のトラフィックが影響を受けている | ワーカールーティングロジックが広すぎます。 | ユーザーエージェントの一致ロジックが正しく、大文字と小文字が区別されないことを検証します。 `TARGETED_PATHS` が正しく設定されていることを確認します。 |
| 応答時間が遅い | Edge での最適化バックエンドに対してネットワーク遅延が発生しています。 | これは最初のリクエストで想定されます。後続のリクエストは、Edge での最適化でキャッシュされます。 |
| 応答の `x-edgeoptimize-fo: 1` ヘッダー | Edge での最適化がエラーを返し、オリジンへのフェールオーバーが発生しました。 | Cloudflare ワーカーのログで特定のエラーコードを確認します。 アドビで Edge での最適化サービスのステータスを検証します。 |
| フェイルオーバーが機能しない | フェイルオーバーフラグが無効になっているか、フェイルオーバーロジックでエラーが発生しています。 | `FAILOVER_ON_4XX` と `FAILOVER_ON_5XX` が `true` に設定されていることを検証します。 ワーカーログにエラーメッセージがないかを確認します。 |
| 特定のパスが最適化されていない | パスがターゲットパスまたは HTML ページパターンと一致していません。 | パスが `TARGETED_PATHS`（指定されている場合）にあり、HTML ページの正規表現パターンと一致していることを検証します。 |
| リクエストが無効なホストで失敗する | `EDGE_OPTIMIZE_TARGET_HOST` にプロトコル（例：`https://`）が含まれています。 | プロトコルなしのドメイン名のみを使用します（例：`https://example.com` ではなく `example.com`）。 |
| フェイルオーバー中の 530 エラー | Cloudflare がオリジンに接続できないか、フェイルオーバーリクエストに無効なヘッダーがあります。 | フェイルオーバー機能で Edge での最適化ヘッダーが削除されていることを確認します。 オリジンがアクセス可能で、DNS が正しく設定されていることを検証します。 |

**ファイアウォール ルールを使用してEdgeで最適化を許可する（オプション）**

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
