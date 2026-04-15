---
title: Edgeで最適化 – Cloudflare （BYOCDN）
description: LLM OptimizerのEdgeでCloudflare BYOCDN for Optimizeを設定する方法について説明します。
feature: Opportunities
source-git-commit: 38ea32e27b1c5c129b019155cb7b717c7ca4f179
workflow-type: tm+mt
source-wordcount: '1922'
ht-degree: 1%

---


# Cloudflare （BYOCDN）

この設定により、エージェント型トラフィック（AI ボットおよびLLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 通常どおり、人間の訪問者とSEO ボットは、元のページから引き続き提供されます。 設定をテストするには、設定が完了した後、応答でヘッダー`x-edgeoptimize-request-id`を探します。

**前提条件**

Cloudflare Workerのルーティングルールを設定する前に、次のことを確認してください。

* ドメインでWorkersが有効になっているCloudflare アカウント。
* CloudflareでドメインのDNS設定にアクセスします。
* LLM Optimizer オンボーディングプロセスを完了しました。
* LLM OptimizerへのCDN ログの転送が完了しました。
* LLM Optimizer UIから取得したEdge Optimize API キー。
* （オプション）ステージング ルーティングをテストするには、このページの最後にある「**オプション：ステージング ホスト名**&#x200B;でのルーティングのテスト」を参照してください。

{{retrieve-byocdn-api-key}}

**ルーティングの仕組み**

正しく設定されると、エージェンティックユーザーエージェントからのドメインへのリクエスト（例えば`www.example.com/page.html`）がCloudflare Workerによって傍受され、Edge Optimize バックエンドにルーティングされます。 バックエンドリクエストには、必要なヘッダーが含まれています。

**バックエンドリクエストのテスト**

Edge Optimize バックエンドに直接リクエストすることで、ルーティングを検証できます。

```
curl -svo /dev/null https://live.edgeoptimize.net/page.html \
  -H 'x-forwarded-host: www.example.com' \
  -H 'x-edgeoptimize-url: /page.html' \
  -H 'x-edgeoptimize-api-key: $EDGE_OPTIMIZE_API_KEY' \
  -H 'x-edgeoptimize-config: LLMCLIENT=TRUE;'
```

**必須ヘッダー**

Edge Optimize バックエンドへのリクエストには、次のヘッダーを設定する必要があります。

| ヘッダー | 説明 | 例 |
|--------|-------------|---------|
| `x-forwarded-host` | リクエストの元のホスト。 サイトドメインの識別に必要です。 | `www.example.com` |
| `x-edgeoptimize-url` | リクエストの元のURL パスとクエリ文字列。 | `/page.html` または `/products?id=123` |
| `x-edgeoptimize-api-key` | Adobeがドメインに対して提供するAPI キー。 | `your-api-key-here` |
| `x-edgeoptimize-config` | キャッシュキーの差別化のための設定文字列。 | `LLMCLIENT=TRUE;` |

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

**手順1: Cloudflare Workerの作成**

1. Cloudflare ダッシュボードにログインします。
2. サイドバーの&#x200B;**Workers &amp; Pages**&#x200B;に移動します。
3. 「**アプリケーションを作成**」をクリックし、**ワーカーを作成**&#x200B;をクリックします。
4. ワーカーに名前を付けます（例：`edge-optimize-router`）。
5. 「**デプロイ**」をクリックして、デフォルトコードを使用してワーカーを作成します。

![Cloudflare Workers ダッシュボード &#x200B;](/help/assets/optimize-at-edge/cloudflare-workers-dashboard.png)

**手順2: ワーカーコードを追加**

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

「**保存してデプロイ**」をクリックして、ワーカーを公開します。

![Cloudflare Worker コードエディター](/help/assets/optimize-at-edge/cloudflare-worker-editor.png)

**手順3：環境変数とシークレットの設定**

環境変数は、API キーなどの機密性の高い設定を安全に保存できます。

1. 作業者の設定で、**設定** > **変数**&#x200B;に移動します。
2. **環境変数**&#x200B;で、**変数を追加**&#x200B;をクリックします。
3. 次の変数を追加します。

   | 変数名 | 説明 | 必須 |
   |---------------|-------------|----------|
   | `EDGE_OPTIMIZE_API_KEY` | Adobeが提供するEdge Optimize API キー。 | はい |
   | `EDGE_OPTIMIZE_TARGET_HOST` | Edge Optimize リクエストのターゲットホスト（`x-forwarded-host` ヘッダーとして送信）とフェールオーバー用のオリジンドメイン。 プロトコルを使用しないドメインのみである必要があります（例：`www.example.com`、`https://www.example.com`ではありません）。 | はい |

4. API キーの場合は、**暗号化**&#x200B;をクリックして安全に保存します。
5. 「**保存してデプロイ**」をクリックします。

![Cloudflare環境変数](/help/assets/optimize-at-edge/cloudflare-env-variables.png)

## ドメインへのルートの追加 {#add-a-route-to-your-domain}

使用した設定オプションに関係なく、ワーカーをドメインに手動でリンクする必要があります。 このステップは、トラフィック上のワーカーをアクティブにします。

1. 作業者の&#x200B;**トリガー** > **設定**&#x200B;に移動します。
2. **ルート**&#x200B;で、**ルートを追加**&#x200B;をクリックします。
3. ドメインパターン（`www.example.com/*`や`example.com/*`など）を入力します。
4. ドロップダウンからゾーンを選択します。
5. 「**保存**」をクリックします。

または、ゾーンレベルでルートを設定することもできます。

1. Cloudflareでドメインに移動します。
2. **Workers Routes**&#x200B;に移動します。
3. 「**ルートを追加**」をクリックし、パターンとワーカーを指定します。

![Cloudflare ワーカーのルート &#x200B;](/help/assets/optimize-at-edge/cloudflare-worker-routes.png)

**フェールオーバー動作の検証**

Edge Optimizeが使用できない場合、またはエラーが返された場合、ワーカーは自動的にオリジンにフェールオーバーします。 フェールオーバー応答には、`x-edgeoptimize-fo` ヘッダーが含まれます。

```
< HTTP/2 200
< x-edgeoptimize-fo: 1
```

Cloudflare Workers ログでフェイルオーバーイベントを監視して、問題をトラブルシューティングできます。

**ワーカーロジックについて**

Cloudflare Workerは、次のロジックを実装します。

1. **ユーザーエージェント検出：**&#x200B;受信リクエストのユーザーエージェントが、定義されたエージェント型ボットのいずれかと一致するかどうかを確認します（大文字と小文字を区別しません）。

2. **パスのターゲット：**&#x200B;必要に応じて、ターゲット設定されたパスに基づいてリクエストをフィルタリングします。 デフォルトでは、すべてのHTML ページ（末尾が`/`、拡張子なし、または`.html`）がルーティングされます。 `TARGETED_PATHS`配列を使用して特定のパスを指定できます。

3. **ループ保護：** `x-edgeoptimize-request` ヘッダーは、無限ループを防ぎます。 Edge Optimizeがオリジンにリクエストを返すと、このヘッダーは`"1"`に設定され、ワーカーはEdge Optimizeにルーティングせずにリクエストを渡します。

4. **ヘッダーのセキュリティ：** Edge Optimize ヘッダーを設定する前に、ワーカーは既存の`x-edgeoptimize-*` ヘッダーを受信リクエストから削除して、ヘッダーインジェクション攻撃を防ぎます。

5. **ヘッダーマッピング：** ワーカーは、Edge Optimizeに必要なヘッダーを設定します。
   * `x-forwarded-host` – 元のサイト ドメインを識別します。
   * `x-edgeoptimize-url` – 元のリクエストパスとクエリ文字列を保持します。
   * `x-edgeoptimize-api-key` - Edge Optimizeでリクエストを認証します。
   * `x-edgeoptimize-config` - キャッシュ キー設定を提供します。

6. **フェールオーバーロジック：** Edge Optimizeがエラーステータスコード（4XX クライアントエラーまたは5XX サーバーエラー）を返すか、ネットワークエラーが原因でリクエストが失敗した場合、ワーカーは`EDGE_OPTIMIZE_TARGET_HOST`を使用してオリジンに自動的にフェールオーバーします。 フェールオーバー応答には、フェールオーバーが発生したことを示す`x-edgeoptimize-fo: 1` ヘッダーが含まれています。

7. **リダイレクト処理：** `redirect: "manual"` オプションを使用すると、Edge Optimizeからのリダイレクト応答が、ワーカーが追いかけることなくクライアントに確実に渡されます。

**設定のカスタマイズ**

コードの上部にある設定定数を変更することで、ワーカーの動作をカスタマイズできます。

**エージェント型ボットリスト**

`AGENTIC_BOTS`配列を変更して、ユーザーエージェントを追加または削除します：

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

**対象パス**

デフォルトでは、すべてのHTML ページがEdge Optimizeにルーティングされます。 ルーティングを特定のパスに制限するには、`TARGETED_PATHS`配列を変更します。

```javascript
// Route all HTML pages (default)
const TARGETED_PATHS = null;

// Or specify exact paths to route
const TARGETED_PATHS = ['/', '/page.html', '/products', '/about-us'];
```

**フェールオーバー設定**

デフォルトでは、ワーカーはEdge Optimizeの4XXまたは5XX エラーでフェールオーバーします。 この動作をカスタマイズ：

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

* **フェールオーバー動作：** Edge Optimizeからエラー（4XXまたは5XX ステータスコード）が返された場合、またはネットワークエラーが原因でリクエストが失敗した場合、ワーカーは自動的にオリジンにフェールオーバーします。 フェールオーバーは、オリジン ドメインとして`EDGE_OPTIMIZE_TARGET_HOST`を使用します（Fastlyの`F_Default_Origin`またはCloudFrontの`Default_Origin`と同様）。 フェールオーバー応答には、`x-edgeoptimize-fo: 1` ヘッダーが含まれており、監視とデバッグに使用できます。

* **キャッシュ：** Cloudflareは、デフォルトでURLに基づいて応答をキャッシュします。 エージェント型トラフィックは人間のトラフィックとは異なるコンテンツを受信するため、キャッシュ設定でこれが考慮されていることを確認してください。 キャッシュされたコンテンツを区別するために、キャッシュ APIまたはキャッシュ ヘッダーの使用を検討してください。 `x-edgeoptimize-config` ヘッダーはキャッシュキーに含める必要があります。

* **レート制限：** Edge Optimizeの使用状況を監視し、必要に応じてエージェント型トラフィックのレート制限を導入することを検討します。

* **テスト：**&#x200B;実稼動にデプロイする前に、必ずステージング環境で設定をテストしてください。 エージェント型トラフィックと人間のトラフィックの両方が期待どおりに動作することを確認します。 Edge Optimize エラーをシミュレートして、フェールオーバーの動作をテストします。

* **ログ：** Cloudflare Workerのログを有効にして、リクエストを監視し、問題をトラブルシューティングします。 **Workers** > **your worker** > **Logs**&#x200B;に移動して、リアルタイム ログを表示します。 ワーカーは、デバッグ目的でフェールオーバーイベントをログに記録します。

**トラブルシューティング**

| 問題 | 考えられる原因 | 解決策 |
|-------|----------------|----------|
| 応答に`x-edgeoptimize-request-id` ヘッダーがありません | ワーカーのルートが一致しないか、エージェント型ボットリストにユーザーエージェントが含まれていません。 | ルートパターンがリクエスト URLと一致することを確認します。 ユーザーエージェントが`AGENTIC_BOTS`配列にあることを確認してください。 |
| Edge Optimizeの401または403 エラー | API キーが無効または見つかりません。 | `EDGE_OPTIMIZE_API_KEY`が環境変数とシークレットで正しく設定されていることを確認します。 API キーがアクティブであることを確認するには、Adobeにお問い合わせください。 |
| 無限リダイレクトまたはループ | ループ保護ヘッダーが正しく設定またはチェックされていません。 | `x-edgeoptimize-request` ヘッダーのチェックが適切に行われていることを確認します。 |
| 人的トラフィックの影響 | 作業者のルーティング ロジックが広すぎます。 | ユーザーエージェントのマッチングロジックが正しく、大文字と小文字を区別しないことを確認します。 `TARGETED_PATHS`が正しく設定されていることを確認してください。 |
| 応答時間が遅い | Edge Optimize バックエンドへのネットワーク遅延。 | これは最初のリクエストで想定されます。後続のリクエストはEdge Optimizeにキャッシュされます。 |
| 応答の`x-edgeoptimize-fo: 1` ヘッダー | Edge Optimizeがエラーを返し、オリジンへのフェールオーバーが発生しました。 | 特定のエラーコードについては、Cloudflare Workersのログを確認してください。 AdobeでEdge Optimize サービスのステータスを確認します。 |
| フェールオーバーが機能しない | フェールオーバーフラグが無効、またはフェールオーバーロジックのエラーです。 | `FAILOVER_ON_4XX`と`FAILOVER_ON_5XX`が`true`に設定されていることを確認します。 ワーカーのログでエラーメッセージを確認します。 |
| 最適化されていない特定のパス | パスがターゲットパスまたはHTML ページパターンと一致しません。 | パスが`TARGETED_PATHS` （指定されている場合）で、HTML ページの正規表現パターンと一致することを確認します。 |
| 無効なホストでリクエストが失敗する | `EDGE_OPTIMIZE_TARGET_HOST`にはプロトコルが含まれています（例：`https://`）。 | プロトコルを使用しないドメイン名のみを使用します（例：`https://example.com`ではなく`example.com`）。 |
| フェールオーバー中の530 エラー | Cloudflareはオリジンに接続できないか、フェールオーバーリクエストに無効なヘッダーが含まれています。 | フェールオーバー関数がEdge Optimize ヘッダーを削除することを確認します。 オリジンがアクセス可能であり、DNSが正しく設定されていることを確認します。 |

**ファイアウォール ルールを使用してEdgeで最適化を許可する（オプション）**

{{waf-allowlist-setup}}

**設定を確認**

設定が完了したら、ボットトラフィックがEdge Optimizeにルーティングされ、人間のトラフィックが影響を受けないことを確認します。

**1. ボットトラフィックのテスト （最適化する必要があります）**

エージェント型ユーザーエージェントを使用してAI ボットリクエストをシミュレートします。

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: chatgpt-user"
```

応答が成功すると、`x-edgeoptimize-request-id` ヘッダーが含まれ、リクエストがEdge Optimizeを通じてルーティングされたことが確認されます。

```
< HTTP/2 200
< x-edgeoptimize-request-id: 50fce12d-0519-4fc6-af78-d928785c1b85
```

**2. 人間のトラフィックをテストします（影響を受けません）**

通常のヒューマンブラウザーリクエストをシミュレートします。

```
curl -svo /dev/null https://www.example.com/page.html \
  --header "user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36"
```

応答には、**not**&#x200B;に`x-edgeoptimize-request-id` ヘッダーを含める必要があります。 Edgeで最適化を有効にする前に、ページの内容と応答時間を同じにしておく必要があります。

**3. 2つのシナリオを区別する方法**

| ヘッダー | ボットトラフィック（最適化） | 人的トラフィック（影響なし） |
|---|---|---|
| `x-edgeoptimize-request-id` | 現在 – 一意のリクエスト IDを含む | 不在 |
| `x-edgeoptimize-fo` | フェールオーバーが発生した場合にのみ存在します（値：`1`） | 不在 |

{{verify-routing-status-in-ui}}

{{retrieve-staging-edge-optimize-api-key}}

```
curl -svo /dev/null https://staging.example.com/page.html \
  --header "user-agent: chatgpt-user"
```

{{return-to-overview}}
