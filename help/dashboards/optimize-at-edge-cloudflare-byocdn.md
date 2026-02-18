---
title: Edgeでの最適化 – Cloudflare （BYOCDN）
description: LLM OptimizerのEdgeで最適化するために Cloudflare BYOCDN を設定する方法を説明します。
feature: Opportunities
source-git-commit: 23752b30294c3d467ca477b085aa521cad0f72ca
workflow-type: tm+mt
source-wordcount: '1250'
ht-degree: 1%

---


# Cloudflare （BYOCDN）

この設定により、エージェンティックトラフィック（AI ボットや LLM ユーザーエージェントからのリクエスト）がEdge Optimize バックエンドサービス（`live.edgeoptimize.net`）にルーティングされます。 人間の訪問者と SEO ボットは、通常どおりオリジンから提供され続けます。 設定をテストするには、セットアップが完了した後、応答でヘッダー `x-edgeoptimize-request-id` を探します。

**前提条件**

Cloudflare Worker のルーティングルールを設定する前に、以下を確認します。

* お使いのドメインでワーカーが有効になっている Cloudflare アカウント。
* Cloudflare でドメインの DNS 設定にアクセスします。
* LLM Optimizerのオンボーディングプロセスを完了しました。
* LLM Optimizerへの CDN ログ転送が完了しました。
* LLM Optimizer UI から取得されたEdge最適化 API キー。

{{retrieve-byocdn-api-key}}

**ルーティングの仕組み**

正しく設定されると、Agentic User Agent からのドメイン（`www.example.com/page.html` など）へのリクエストが Cloudflare Worker によってインターセプトされ、Edge Optimize バックエンドにルーティングされます。 バックエンドリクエストには、必要なヘッダーが含まれます。

**バックエンドリクエストのテスト**

Edgeの Optimize バックエンドに対して直接リクエストを行うことで、ルーティングを確認できます。

```
curl -svo /dev/null https://live.edgeoptimize.net/page.html \
  -H 'x-forwarded-host: www.example.com' \
  -H 'x-edgeoptimize-url: /page.html' \
  -H 'x-edgeoptimize-api-key: $EDGE_OPTIMIZE_API_KEY' \
  -H 'x-edgeoptimize-config: LLMCLIENT=TRUE;'
```

**必須ヘッダー**

Edge Optimize バックエンドへのリクエストでは、次のヘッダーを設定する必要があります。

| ヘッダー | 説明 | 例 |
|--------|-------------|---------|
| `x-forwarded-host` | リクエストの元のホスト。 サイトドメインを識別するために必要です。 | `www.example.com` |
| `x-edgeoptimize-url` | リクエストの元の URL パスとクエリ文字列。 | `/page.html` または `/products?id=123` |
| `x-edgeoptimize-api-key` | ドメイン用にAdobeから提供される API キー。 | `your-api-key-here` |
| `x-edgeoptimize-config` | キャッシュキーを区別するための設定文字列。 | `LLMCLIENT=TRUE;` |

**手順 1:Cloudflare Worker の作成**

1. Cloudflare ダッシュボードにログインします。
2. サイドバーの **ワーカーとページ** に移動します。
3. **アプリケーションを作成** をクリックしてから、**ワーカーを作成** をクリックします。
4. ワーカーに名前を付けます（例：`edge-optimize-router`）。
5. 「**デプロイ**」をクリックして、デフォルトのコードを使用してワーカーを作成します。

![Cloudflare Workers ダッシュボード &#x200B;](/help/assets/optimize-at-edge/cloudflare-workers-dashboard.png)

**手順 2：ワーカーコードの追加**

ワーカーを作成したら、「**コードを編集**」をクリックして、デフォルトのコードを以下に置き換えます。

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

    // x-forwarded-host: The original site domain
    // Use environment variable if set, otherwise use the request host
    edgeOptimizeHeaders.set("x-forwarded-host", env.EDGE_OPTIMIZE_TARGET_HOST ?? url.host);

    // x-edgeoptimize-api-key: Your Adobe-provided API key
    edgeOptimizeHeaders.set("x-edgeoptimize-api-key", env.EDGE_OPTIMIZE_API_KEY);

    // x-edgeoptimize-url: The original request URL path and query
    edgeOptimizeHeaders.set("x-edgeoptimize-url", pathAndQuery);

    // x-edgeoptimize-config: Configuration for cache key differentiation
    edgeOptimizeHeaders.set("x-edgeoptimize-config", "LLMCLIENT=TRUE;");

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

**保存してデプロイ** をクリックして、ワーカーを公開します。

![Cloudflare Worker コードエディター &#x200B;](/help/assets/optimize-at-edge/cloudflare-worker-editor.png)

**手順 3：環境変数の設定**

環境変数は、API キーなどの機密性の高い設定を安全に保存します。

1. ワーカーの設定で、**設定**/**変数** に移動します。
2. **環境変数** の下の **変数を追加** をクリックします。
3. 次の変数を追加します。

   | 変数名 | 説明 | 必須 |
   |---------------|-------------|----------|
   | `EDGE_OPTIMIZE_API_KEY` | Adobe提供のEdge Optimize API キー。 | はい |
   | `EDGE_OPTIMIZE_TARGET_HOST` | Edge Optimize リクエスト（ヘッダーとして送信）のターゲットホスト `x-forwarded-host`、フェイルオーバーのオリジンドメイン。 プロトコルを使用しないドメインのみを指定する必要があります（例：`https://www.example.com` ではなく `www.example.com`）。 | はい |

4. API キーの場合は、「**暗号化**」をクリックして、安全に保存します。
5. **保存してデプロイ** をクリックします。

![Cloudflare 環境変数 &#x200B;](/help/assets/optimize-at-edge/cloudflare-env-variables.png)

**手順 4：ドメインへのルートの追加**

ドメインでワーカーをアクティブ化するには：

1. ワーカーの **設定**/**トリガー** に移動します。
2. **ルート** の下の **ルートを追加** をクリックします。
3. ドメインパターン（例：`www.example.com/*` または `example.com/*`）を入力します。
4. ドロップダウンからゾーンを選択します。
5. 「**保存**」をクリックします。

または、ゾーン レベルでルートを構成することもできます。

1. Cloudflare のドメインに移動します。
2. **ワーカールート** に移動します。
3. **ルートを追加** をクリックし、パターンとワーカーを指定します。

![Cloudflare ワーカールート &#x200B;](/help/assets/optimize-at-edge/cloudflare-worker-routes.png)

**フェイルオーバー動作の確認**

Edge Optimize が使用できない場合やエラーが返された場合、ワーカーは自動的にオリジンにフェイルオーバーします。 フェイルオーバー応答には、`x-edgeoptimize-fo` ヘッダーが含まれます。

```
< HTTP/2 200
< x-edgeoptimize-fo: 1
```

Cloudflare Workers ログでフェイルオーバーイベントを監視して、問題のトラブルシューティングを行うことができます。

**ワーカーロジックについて**

Cloudflare Worker は、次のロジックを実装します。

1. **ユーザーエージェントの検出：** 受信リクエストのユーザーエージェントが、定義されたエージェンティックボットのいずれかと一致するかどうかを確認します（大文字と小文字を区別しません）。

2. **パスのターゲット設定：** オプションで、ターゲットパスに基づいてリクエストをフィルタリングします。 デフォルトでは、すべてのHTML ページ（`/`、拡張子なし、`.html` で終わる URL）がルーティングされます。 `TARGETED_PATHS` 配列を使用して、特定のパスを指定できます。

3. **ループ保護：** `x-edgeoptimize-request` ヘッダーは、無限ループを防ぎます。 Edge Optimize がオリジンにリクエストを戻す場合、このヘッダーは `"1"` に設定され、ワーカーはEdge Optimize にリクエストをルーティングせずに通過させます。

4. **Header security:** Edge Optimize ヘッダーを設定する前に、Header Injection 攻撃を防ぐために、ワーカーは受信リクエストから既存の `x-edgeoptimize-*` ヘッダーを削除します。

5. **ヘッダーマッピング：** ワーカーは、Edge Optimize に必要なヘッダーを設定します。
   * `x-forwarded-host` – 元のサイトドメインを識別します。
   * `x-edgeoptimize-url` – 元のリクエストパスとクエリ文字列を保持します。
   * `x-edgeoptimize-api-key` - Edge Optimize でリクエストを認証します。
   * `x-edgeoptimize-config` - キャッシュキーを設定します。

6. **フェイルオーバーロジック：** Edge Optimize からエラーステータスコード（4XX クライアントエラーまたは 5XX サーバーエラー）が返された場合、またはネットワークエラーが原因でリクエストが失敗した場合、ワーカーは `EDGE_OPTIMIZE_TARGET_HOST` を使用して、自動的にオリジンにフェイルオーバーします。 フェイルオーバー応答には、フェイルオーバーが発生したことを示す `x-edgeoptimize-fo: 1` ヘッダーが含まれます。

7. **リダイレクト処理：** 「`redirect: "manual"`」オプションを使用すると、Edge Optimize からのリダイレクト応答が、ワーカーが従わずにクライアントに確実に渡されます。

**設定のカスタマイズ**

ワーカーの動作をカスタマイズするには、コードの上部にある設定定数を変更します。

**Agentic ボットリスト**

ユーザーエージェントを追加または削除するには、`AGENTIC_BOTS` のアレイを変更します。

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

デフォルトでは、すべてのHTMLページがEdge Optimize にルーティングされます。 特定のパスへのルーティングを制限するには、`TARGETED_PATHS` の配列を変更します。

```javascript
// Route all HTML pages (default)
const TARGETED_PATHS = null;

// Or specify exact paths to route
const TARGETED_PATHS = ['/', '/page.html', '/products', '/about-us'];
```

**フェイルオーバー構成**

デフォルトでは、Edge Optimize から 4XX または 5XX のエラーが発生した場合にワーカーがフェイルオーバーします。 この動作をカスタマイズします。

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

* **フェイルオーバー動作：** Edge Optimize からエラー（4XX または 5XX ステータスコード）が返された場合、またはネットワークエラーが原因でリクエストが失敗した場合、ワーカーは自動的にオリジンにフェイルオーバーします。 フェイルオーバーでは、`EDGE_OPTIMIZE_TARGET_HOST` をオリジンドメインとして使用します（Fastly の `F_Default_Origin` や CloudFront の `Default_Origin` と同様）。 フェイルオーバー応答には、監視とデバッグに使用できる `x-edgeoptimize-fo: 1` ヘッダーが含まれています。

* **キャッシュ：** Cloudflare は、デフォルトで URL に基づいて応答をキャッシュします。 エージェンティックトラフィックは人間によるトラフィックとは異なるコンテンツを受け取るので、キャッシュ設定に責任があることを確認してください。 キャッシュ API またはキャッシュヘッダーを使用して、キャッシュされたコンテンツを区別することを検討します。 `x-edgeoptimize-config` ヘッダーをキャッシュキーに含める必要があります。

* **レート制限：** Edgeのモニタリング使用状況を最適化し、必要に応じてエージェンティックトラフィックにレート制限を実装することを検討します。

* **テスト：** 実稼動環境にデプロイする前に、常にステージング環境で設定をテストします。 エージェンシーと人間の両方のトラフィックが期待どおりに動作することを確認します。 Edge Optimize エラーをシミュレートして、フェイルオーバー動作をテストします。

* **ログ：** Cloudflare Workers のログを有効にして、リクエストの監視や問題のトラブルシューティングを行います。 **ワーカー** / **ワーカー** / **ログ** に移動して、リアルタイムログを表示します。 ワーカーは、デバッグ目的でフェイルオーバーイベントをログに記録します。

**トラブルシューティング**

| 問題 | 考えられる原因 | 解決策 |
|-------|----------------|----------|
| 応答に `x-edgeoptimize-request-id` ヘッダーがありません | 作業者ルートが一致していないか、エージェント ボット リストにユーザーエージェントがありません。 | ルートパターンがリクエスト URL と一致することを確認します。 ユーザーエージェントが `AGENTIC_BOTS` 配列にあることを確認します。 |
| Edge Optimize の 401 または 403 エラー | API キーが無効または見つかりません。 | 環境変数 `EDGE_OPTIMIZE_API_KEY` 正しく設定されていることを確認します。 Adobeに問い合わせて、API キーがアクティブであることを確認します。 |
| 無限リダイレクトまたはループ | ループ保護ヘッダーが正しく設定またはチェックされていない。 | `x-edgeoptimize-request` ヘッダーチェックが行われていることを確認します。 |
| 影響を受ける人的トラフィック | ワーカールーティングロジックが広すぎます。 | ユーザーエージェントのマッチングロジックが正しく、大文字と小文字が区別されないことを確認します。 `TARGETED_PATHS` が正しく設定されていることを確認します。 |
| 応答時間が遅い | Edgeへのネットワーク遅延バックエンドを最適化します。 | これは最初のリクエストで想定されます。後続のリクエストは、Edge Optimize でキャッシュされます。 |
| 応答の `x-edgeoptimize-fo: 1` ヘッダー | Edge Optimize がエラーを返し、オリジンへのフェールオーバーが発生しました。 | 特定のエラーコードについては、Cloudflare Workers のログを確認してください。 AdobeでEdge最適化サービスのステータスを確認します。 |
| フェイルオーバーが機能しない | フェールオーバーフラグが無効になっているか、フェールオーバーロジックでエラーが発生しています。 | `FAILOVER_ON_4XX` と `FAILOVER_ON_5XX` が `true` に設定されていることを確認します。 ワーカーログでエラーメッセージを確認します。 |
| 特定のパスが最適化されていない | ターゲットパスまたはHTMLページパターンと一致しないパス。 | パスが `TARGETED_PATHS` 内にあり（指定されている場合）、HTML ページの正規表現パターンと一致することを確認します。 |
| リクエストが無効なホストで失敗する | `EDGE_OPTIMIZE_TARGET_HOST` にはプロトコル（例：`https://`）が含まれます。 | プロトコルを使用しないドメイン名のみを使用します（例：`https://example.com` ではなく `example.com`）。 |
| フェイルオーバー時の 530 エラー | Cloudflare がオリジンに接続できないか、フェイルオーバーリクエストに無効なヘッダーがあります。 | フェイルオーバー機能によってEdge Optimize ヘッダーが削除されるようにします。 オリジンがアクセス可能であり、DNS が正しく設定されていることを確認します。 |

{{verify-setup-byocdn}}

{{return-to-overview}}
