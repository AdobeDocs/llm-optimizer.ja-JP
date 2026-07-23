---
title: Edgeでの最適化 – Apache HTTP Server
description: LLM OptimizerのEdgeでOptimize用にApache HTTP Server （セルフホスト型リバースプロキシ） BYOCDNを設定する方法について説明します。
feature: Opportunities
product_v2:
  - id: d830747e-f8f3-4fce-8eff-d53b333b1639
feature_v2:
  - id: d1956731-2adb-4bb7-8301-2b239254ac72
subfeature_v2:
  - id: d23587d6-14d6-4e3f-9ee1-cc18623832e1
source-git-commit: d7e723161836027dcdde931378f5d0f776a1ecfc
workflow-type: tm+mt
source-wordcount: 585
ht-degree: 32%

---


# Apache HTTP サーバー

この設定は、Apache HTTP Serverがオリジンの前でリバースプロキシとして機能する場合（セルフホスト設定、**AEM Dispatcherなし）に適用されます。**&#x200B;エージェント型トラフィック （AI ボットおよびLLM ユーザーエージェントからのリクエスト）をEdge Optimize バックエンドサービス （`live.edgeoptimize.net`）にルーティングします。 人間の訪問者と SEO ボットは、通常どおりオリジンから引き続き提供されます。 設定をテストするには、設定が完了したら、応答のヘッダー `x-edgeoptimize-request-id` を探します。

統合は、ネイティブ Apache `Include` ファイルのセットです。デプロイするコードやワーカーはありません。 3つのファイルをダウンロードし、API キーを設定し、仮想ホストに2つの`Include`行を追加します。

**前提条件**

Apache ルーティングルールを設定する前に、次のことを確認してください。

* これらのモジュールが有効になっているApache HTTP Server 2.4以降：`proxy`、`proxy_http`、`ssl`、`rewrite`、`headers`、`env`、および`setenvif`。
* Apache設定（サイトの`<VirtualHost>`）へのアクセスと、Apacheをリロードする機能。
* LLM Optimizer UI から取得された Edge Optimize API キー。 手順について詳しくは、[API キーの取得](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#production-api-key)を参照してください。
* （オプション）ステージングルーティングをテストするには、[Staging API キー](/help/dashboards/optimize-at-edge/retrieve-api-keys.md#staging-api-key-optional)を参照してください。

## 設定

**1. 設定ファイルをダウンロード**

[Optimize at Edge code samples リポジトリ &#x200B;](https://github.com/adobe/llmo-code-samples/tree/main/optimize-at-edge/apache)から3つのEdge Optimize インクルードファイルをダウンロードし、Apache サーバー上のディレクトリに配置します（例：`conf/oae/`）。

| ファイル | 目的 |
|------|---------|
| `oae-routing.conf` | AI ボットを検出し、Edge Optimize ヘッダーを挿入し、HTML ページリクエストをバックエンドにルーティングし、キャッシュの分離とフェールオーバーを設定します。 |
| `oae-failover.conf` | Edge Optimizeがエラーを返した場合、元のリクエストをオリジンに対して再生します。 |
| `domains.conf` | ドメインごとにEdgeで最適化を有効にし、API キーを保持します。 |

`oae-routing.conf`または`oae-failover.conf`を変更する必要はありません。そのまま使用してください。

**2. ドメインを有効にし、API キー（`domains.conf`）**&#x200B;を設定します

`domains.conf`を編集し、有効にするドメインごとに1行を追加します。 ホストをドメインに置き換え、`YOUR_API_KEY`をLLM Optimizer UIのキーに置き換えます。 ドメインはオリジンへのルートが変更されていないため、一度に1つのドメインを有効にできます。

```
SetEnvIfExpr "%{HTTP_HOST} =~ m#(?i)^(www\.)?example\.com(:\d+)?$#" OAE_DOMAIN_ENABLED=1 OAE_API_KEY=YOUR_API_KEY
```

**3. 仮想ホストにファイルを含める**

既存の`<VirtualHost *:443>`に2行の`Include`行を追加します。 ルーティングファイルは書き換え前&#x200B;**前**&#x200B;および`ProxyPass` ルールに移行します。フェイルオーバーファイルは&#x200B;**後**&#x200B;に移行します。 次の例では、`#NEWLINE`とマークされた行は、Edgeで最適化するために追加した唯一の行です。それ以外のすべて（`ServerName`、`ProxyPass`、および残りの行）は、既存の未変更の設定です。

```
Define OAE_CONF_DIR conf/oae                       #NEWLINE  directory holding the OAE include files

<VirtualHost *:443>
    ServerName www.example.com

    Include "${OAE_CONF_DIR}/oae-routing.conf"     #NEWLINE  OAE routing — BEFORE your Rewrite & ProxyPass rules

    # --- your existing rewrite rules and ProxyPass to origin ---
    ProxyPass        "/" "https://www.example.com/"
    ProxyPassReverse "/" "https://www.example.com/"

    Include "${OAE_CONF_DIR}/oae-failover.conf"    #NEWLINE  OAE failover — AFTER your ProxyPass rules
</VirtualHost>
```

**4. Apache**&#x200B;をリロード

設定を検証し、Apacheをリロードして変更を適用します。

>[!NOTE]
>
>ボットに最適化された応答と人間の応答は、自動的に別々のキャッシュ エントリに保持されます（ルーティング ファイル セット `Vary: x-edgeoptimize-config`）。 Apacheで既に`mod_cache`を使用している場合は、Edge Optimize ヘッダーが設定された後でキャッシュ検索が実行されるように、`CacheQuickHandler Off`が含まれていることを確認します。

## ファイアウォールルールによるEdgeでの最適化を許可する（オプション）

{{waf-allowlist-setup}}

## 設定の検証

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
