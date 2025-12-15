---
title: Edgeで最適化
description: オーサリングを変更せずに、CDN エッジでLLM Optimizerで最適化を配信する方法を説明します。
feature: Opportunities
source-git-commit: 522abddcabaf02ce86fb4d7978ef7fa4d6eb0358
workflow-type: tm+mt
source-wordcount: '2218'
ht-degree: 1%

---


# Edgeで最適化

このページでは、オーサリングを変更せずに CDN エッジで最適化を配信する方法の詳細な概要を説明します。 オンボーディングプロセス、利用可能な最適化の機会、Edge で自動最適化する方法について説明します。

>[!NOTE]
>この機能は、現在、早期アクセス中です。

## Edgeでの最適化とは

Edgeでの最適化は、LLM ユーザーエージェントに対して AI に適した変更を提供する、LLM Optimizerのエッジベースのデプロイメント機能です。 現在のコンテキストでは、「Edge」は、最適化が CDN レイヤーで適用されることを意味します。 CDN レイヤーで最適化が行われるので、元のCMSが変わらないように、Content Management System （CMS）でのオーサリング変更は必要ありません。 この分離により、既存の公開ワークフローを変更することなく、LLM の可視性を向上させることができます。 これはエージェンティックトラフィックのみをターゲットにし、人間のユーザーや SEO ボットには影響しません。 LLM Optimizerがページを最適化する機会を検出すると、ユーザーは CDN エッジに直接修正をデプロイできます。

Edgeでの最適化は、複雑なエンジニアリング作業を必要とする従来の修正に代わる、より迅速で無駄のない方法です。 前述のように、1 回限りの設定を完了すると、変更を適用するためにプラットフォームの変更や長い開発サイクルが必要になることはありません。 開発者のエンゲージメントを必要とせずに、改善点を数分で公開できます。 これは、AI エージェントのために web サイトを最適化するための、リスクの低いコードなしの方法です。

Edgeでの最適化は、マーケティング、SEO、コンテンツ、デジタル戦略の各チームのビジネスユーザー向けに設計されています。 ビジネスユーザーはLLM Optimizerで、オポチュニティの特定、提案の理解、簡単な修正点のデプロイなどのすべてのジャーニーを完了できます。 Edgeでの最適化を使用すると、変更をプレビューし、CDN エッジですばやくデプロイして、最適化がライブであることを検証できます。 パフォーマンスは、LLM Optimizerのエコシステムでトラッキングできます。

### 主なメリット

* **AI のみの配信：** 最適化されたHTMLを AI エージェントにのみ提供し、人間の訪問者や SEO ボットには影響しません。
* **サイクルの高速化：** 変更を数週間ではなく数分で公開します。 プラットフォームの変更や長いエンジニアリングサイクルは不要です。
* **低リスクで可逆的：** ページを数分で元に戻せるワンクリックロールバック機能がサポートされています。
* **パフォーマンスへの影響なし：** Edge ベースの最適化とキャッシュにより、サイトの待ち時間に影響しません。
* **CDN とCMSに依存しない：** コンテンツ管理システムに関係なく、任意の CDN 設定およびフロントエンド設定と連携します。

### Edgeでの最適化でサポートされているオポチュニティを教えてください。

Edgeの最適化では、効率的な web エクスペリエンスを実現するオポチュニティがサポートされています。 [&#x200B; 商談ダッシュボード &#x200B;](/help/dashboards/opportunities.md) ページの各商談について、および現在のページの商談セクションについて詳しく説明します。

## オンボーディング

オンボーディングプロセスを開始するには、Adobe アカウントチームまたは FDE チームに連絡する必要があります。 IT または CDN チームも、前提条件とセットアッププロセスを完了する必要があります。 さらに、`llmo-at-edge@adobe.com` のチームに連絡して、さらにオンボーディングサポートを受けることもできます。

Edgeで Optimize にオンボーディングするための前提条件：

* LLM Optimizerのオンボーディングプロセスを完了します。
* CDN ログのログ転送プロセスを完了します。

IT/CDN チームの要件：

* API キーを生成します。
* CDN のEdge ルーティングルールで最適化を追加します。
* ユーザー定義のパスまたはドメイン全体を許可リストに加えるします。
* ユーザー定義の LLM ユーザー・エージェント・リストをターゲットに追加します。
* ターゲッ `robots.txt` ング対象のユーザーエージェントがブロックされていないことを確認します。
* LLM Optimizer インターフェイスでEdge ルーティングの最適化を確定します。

設定プロセスをガイドするために、以下に示すのは、様々な CDN 設定のサンプル設定です。 これらの例は、実際のライブ設定に合わせて調整してください。 最初に下位環境で変更を適用することをお勧めします。

>[!NOTE]
>以下のコードサンプルでは、Edgeの Optimize の作業プロジェクト名である「tokowaka」への参照が確認できます。 これらの識別子は、互換性のためにコード内に残り、このドキュメントで説明したのと同じ機能を参照します。

>[!BEGINTABS]

>[!TAB Adobeの管理による CDN]

**Adobeの管理による CDN**

この設定の目的は、Optimizer サービス（バックエンド）にルーティングされるエージェントのユーザーエージェントを使用してリクエストを設定 `edge.tokowaka.now` ることです。 設定をテストするには、セットアップが完了した後、応答でヘッダー `x-tokowaka-request-id` を探します。

```
curl -svo page.html https://frescopa.coffee/about-us --header "user-agent: chatgpt-user"
< HTTP/2 200
< x-tokowaka-request-id: 50fce12d-0519-4fc6-af78-d928785c1b85
```

ルーティング設定は、[originSelector CDN ルール &#x200B;](https://experienceleague.adobe.com/ja/docs/experience-manager-cloud-service/content/implementing/content-delivery/cdn-configuring-traffic#origin-selectors) を使用して行われます。 前提条件は次のとおりです。

* ルーティングするドメインの決定
* ルーティングするパスの決定
* ルーティングするユーザーエージェントを決定する（推奨の正規表現）
* `edge.tokowaka.now` バックエンド用の api キーをAdobeから取得する

ルールをデプロイするには、次の操作が必要です。

* [&#x200B; 設定パイプライン &#x200B;](https://experienceleague.adobe.com/ja/docs/experience-manager-cloud-service/content/operations/config-pipeline) を作成
* リポジトリ内の `cdn.yaml` 設定ファイルをコミットします
* api キーを [&#x200B; シークレット環境変数 &#x200B;](https://experienceleague.adobe.com/ja/docs/experience-manager-cloud-service/content/implementing/content-delivery/cdn-credentials-authentication) としてデプロイします
* 設定パイプラインを実行


```
kind: "CDN"
version: "1"
data:
  # Origin selectors to route to Tokowaka backend
  originSelectors:
    rules:
      - name: route-to-tokowaka-backend
        when:
          allOf:
            - reqHeader: x-tokowaka-request
              exists: false # avoid loops when requests comes from Tokowaka
            - reqHeader: user-agent
              matches: "(?i)(Tokowaka-AI|ChatGPT-User|GPTBot|OAI-SearchBot|PerplexityBot|Perplexity-User)" # routed user agents
            - reqProperty: domain
              equals: "example.com" # routed domain
            - reqProperty: originalPath
              matches: '(/[^./]+|\.html|/)$' # routed extensions, with .html extension or without extension
            - anyOf:
              - { reqProperty: originalPath, in: [ "/page.html" ] } # routed pages, exact path matching
              - { reqProperty: originalPath, like: "/dir/*" } # routed pages, wildcard path matching
        action:
          type: selectOrigin
          originName: tokowaka-backend
          headers:
            x-tokowaka-api-key: "${{TOKOWAKA_API_KEY}}"
    origins:
      - name: tokowaka-backend
        domain: "edge.tokowaka.now"
```

設定をテストするには、curl を実行し、次の内容を確認します。

```
curl -svo page.html https://www.example.com/page.html --header "user-agent: chatgpt-user"
< HTTP/2 200
< x-tokowaka-request-id: 50fce12d-0519-4fc6-af78-d928785c1b85
```

<!-- >>[!TAB Akamai (BYOCDN)]

**Tokowaka BYOCDN - Akamai**

```
{
    "name": "Project Tokowaka CDN Rule",
    "children": [
        {
            "name": "Connection settings",
            "children": [],
            "behaviors": [
                {
                    "name": "advanced",
                    "options": {
                        "description": "",
                        "xml": "<forward:availability.health-detect.status>off</forward:availability.health-detect.status>\n<forward:availability>\n<max-reforwards>1</max-reforwards>\n<max-reconnects>1</max-reconnects>\n</forward:availability>\n<match:forward.server-type value=\"CUSTOMER_ORIGIN\">\n<network:http.read>%(PMUSER_HTTP_READ)</network:http.read>\n<network:http.first-byte-timeout>%(PMUSER_FIRST_BYTE_TIMEOUT)</network:http.first-byte-timeout>\n<network:http.connect-timeout>%(PMUSER_HTTP_CONNECT_TIMEOUT)</network:http.connect-timeout> \n</match:forward.server-type>"
                    },
                    "uuid": "4a8c027b-1b23-44a7-8e12-f8d07e453679",
                    "templateUuid": "41c77091-419f-43f2-9a84-0b406b050cc8"
                }
            ],
            "uuid": "4759571b-8036-4c16-9b60-d3aeb84958f1",
            "criteria": [],
            "criteriaMustSatisfy": "all"
        },
        {
            "name": "Site Failover Behavior",
            "children": [],
            "behaviors": [
                {
                    "name": "failAction",
                    "options": {
                        "actionType": "RECREATED_CO",
                        "contentCustomPath": false,
                        "contentHostname": "www.adobe.com",
                        "enabled": true
                    }
                },
                {
                    "name": "advanced",
                    "options": {
                        "description": "",
                        "xml": "<forward:availability.fail-action2>\n<add-header>\n<status>on</status>\n<name>x-tokowaka-request</name>\n<value>fo</value>\n</add-header>\n</forward:availability.fail-action2>"
                    }
                }
            ],
            "uuid": "b3000c12-1ab8-49b1-a5d0-75e58bb18c9c",
            "criteria": [
                {
                    "name": "matchResponseCode",
                    "options": {
                        "lowerBound": 400,
                        "matchOperator": "IS_BETWEEN",
                        "upperBound": 599
                    }
                },
                {
                    "name": "originTimeout",
                    "options": {
                        "matchOperator": "ORIGIN_TIMED_OUT"
                    }
                }
            ],
            "criteriaMustSatisfy": "any",
            "comments": "If Tokowaka origin returns a 4xx or 5xx error (or times out), failover condition is to send the request back to Akamai and set the x-tokowaka-request header so we don't re-send the request to Tokowaka"
        }
    ],
    "behaviors": [
        {
            "name": "origin",
            "options": {
                "cacheKeyHostname": "ORIGIN_HOSTNAME",
                "compress": true,
                "customValidCnValues": [
                    "{{Origin Hostname}}",
                    "{{Forward Host Header}}",
                    "*.tokowaka.now"
                ],
                "enableTrueClientIp": true,
                "forwardHostHeader": "ORIGIN_HOSTNAME",
                "hostname": "edge.tokowaka.now",
                "httpPort": 80,
                "httpsPort": 443,
                "ipVersion": "IPV4",
                "minTlsVersion": "DYNAMIC",
                "originCertificate": "",
                "originCertsToHonor": "STANDARD_CERTIFICATE_AUTHORITIES",
                "originSni": true,
                "originType": "CUSTOMER",
                "ports": "",
                "standardCertificateAuthorities": [
                    "akamai-permissive",
                    "THIRD_PARTY_AMAZON"
                ],
                "tlsVersionTitle": "",
                "trueClientIpClientSetting": true,
                "trueClientIpHeader": "True-Client-IP",
                "verificationMode": "CUSTOM"
            }
        },
        {
            "name": "setVariable",
            "options": {
                "transform": "NONE",
                "valueSource": "EXPRESSION",
                "variableName": "PMUSER_LLMCLIENT",
                "variableValue": "TRUE"
            }
        },
        {
            "name": "setVariable",
            "options": {
                "caseSensitive": false,
                "extractLocation": "CLIENT_REQUEST_HEADER",
                "globalSubstitution": false,
                "headerName": "Accept-Language ",
                "regex": "^([a-zA-Z]{2}).*",
                "replacement": "$1",
                "transform": "SUBSTITUTE",
                "valueSource": "EXTRACT",
                "variableName": "PMUSER_LANG"
            }
        },
        {
            "name": "setVariable",
            "options": {
                "transform": "NONE",
                "valueSource": "EXPRESSION",
                "variableName": "PMUSER_X_FORWARDED_HOST",
                "variableValue": "{{builtin.AK_HOST}}"
            }
        },
        {
            "name": "setVariable",
            "options": {
                "transform": "NONE",
                "valueSource": "EXPRESSION",
                "variableName": "PMUSER_TOKOWAKA_CACHE_KEY",
                "variableValue": "LLMCLIENT={{user.PMUSER_LLMCLIENT}};LANG={{user.PMUSER_LANG}};X_FORWARDED_HOST={{user.PMUSER_X_FORWARDED_HOST}}"
            }
        },
        {
            "name": "caching",
            "options": {
                "behavior": "CACHE_CONTROL_AND_EXPIRES",
                "cacheControlDirectives": "",
                "defaultTtl": "1d",
                "enhancedRfcSupport": false,
                "honorMustRevalidate": false,
                "honorPrivate": false,
                "mustRevalidate": false
            }
        },
        {
            "name": "modifyIncomingRequestHeader",
            "options": {
                "action": "MODIFY",
                "avoidDuplicateHeaders": true,
                "customHeaderName": "X-tokowaka-api-key",
                "newHeaderValue": "<your api-key here>",
                "standardModifyHeaderName": "OTHER"
            }
        },
        {
            "name": "modifyIncomingRequestHeader",
            "options": {
                "action": "MODIFY",
                "avoidDuplicateHeaders": true,
                "customHeaderName": "x-tokowaka-config",
                "newHeaderValue": "LLMCLIENT={{user.PMUSER_LLMCLIENT}};LANG={{user.PMUSER_LANG}}",
                "standardModifyHeaderName": "OTHER"
            }
        },
        {
            "name": "modifyIncomingRequestHeader",
            "options": {
                "action": "MODIFY",
                "avoidDuplicateHeaders": true,
                "customHeaderName": "x-tokowaka-url",
                "newHeaderValue": "{{builtin.AK_URL}}",
                "standardModifyHeaderName": "OTHER"
            }
        },
        {
            "name": "cacheId",
            "options": {
                "rule": "INCLUDE_VARIABLE",
                "variableName": "PMUSER_TOKOWAKA_CACHE_KEY"
            }
        },
        {
            "name": "modifyIncomingResponseHeader",
            "options": {
                "action": "DELETE",
                "customHeaderName": "Age",
                "standardDeleteHeaderName": "OTHER"
            }
        },
        {
            "name": "prefreshCache",
            "options": {
                "enabled": true,
                "prefreshval": 90
            }
        },
        {
            "name": "modifyOutgoingRequestHeader",
            "options": {
                "action": "MODIFY",
                "avoidDuplicateHeaders": true,
                "customHeaderName": "X-Forwarded-Host",
                "newHeaderValue": "{{builtin.AK_HOST}}",
                "standardModifyHeaderName": "OTHER"
            }
        }
    ],
    "criteria": [
        {
            "name": "userAgent",
            "options": {
                "matchCaseSensitive": false,
                "matchOperator": "IS_ONE_OF",
                "matchWildcard": true,
                "values": [
                    "*Tokowaka-AI*",
                    "*ChatGPT-User*",
                    "*GPTBot*",
                    "*OAI-SearchBot*"
                ]
            }
        },
        {
            "name": "path",
            "options": {
                "matchCaseSensitive": false,
                "matchOperator": "MATCHES_ONE_OF",
                "normalize": false,
                "values": [
                ]
            }
        },
        {
            "name": "requestHeader",
            "options": {
                "headerName": "x-tokowaka-request",
                "matchOperator": "DOES_NOT_EXIST",
                "matchWildcardName": false
            }
        },
        {
            "name": "matchVariable",
            "options": {
                "matchCaseSensitive": true,
                "matchOperator": "IS",
                "matchWildcard": false,
                "variableExpression": "FALSE",
                "variableName": "PMUSER_TOKOWAKA_DISABLE"
            }
        }
    ],
    "criteriaMustSatisfy": "all"
}
```

Important considerations:

* Tokowaka Rule will be ON based on User-Agent + Path + x-tokowaka-request (if not present) + TOKOWAKA_DISABLE variable (to allow switch off using a single variable toggle)
* Set up rules to **Modify Incoming Request Headers** rule to set custom headers
* Set cache-key in Akamai using user defined variable through Cache-ID modification mechanism. Only a single user defined variable is allowed, so create a separate variable for cache_key and set it accordingly.
* Lang: extracted from Accept-Language header using "regex": "^([a-zA-Z]{2}).*"
* With Cache ID Modification within a match on User Agent, the content can't be purged by URL (just FYI)
* Site failover mechanism: With the match on User-Agent rule, Akamai does not allows to failover based on health check, but only only basis of origin response/connectivity per request. Set **x-tokowaka-fo:true**  resp header in case of failover response.
* SWR is not supported by Akamai. So, only TTL based caching is there. So, configure a rule in Akamai to strip Age header from origin response else TTL based caching would not work.
* Ensure that the Tokowaka rule is the bottom most rule in the rule hierarchy (so that it overrides all other rules).-->

>[!TAB Fastly （BYOCDN） ]

**常若 BYOCDN - Fastly - VCL**

![Fastly VCL](/help/assets/optimize-at-edge/fastly-vcl.png)

![VCL スニペットの追加 &#x200B;](/help/assets/optimize-at-edge/add-vcl-snippets.png)

**vcl_recv スニペット**

```
unset req.http.x-tokowaka-url;
unset req.http.x-tokowaka-config;
unset req.http.x-tokowaka-api-key;

if (!req.http.x-tokowaka-request
    && req.http.user-agent ~ "(?i)(Tokowaka-AI|ChatGPT-User|GPTBot|OAI-SearchBot|PerplexityBot|Perplexity-User)") {
  set req.http.x-fowarded-host = req.http.host; # required for identifying the original host
  set req.http.x-tokowaka-url = req.url; # required for identifying the original url
  set req.http.x-tokowaka-config = "LLMCLIENT=true"; # required for cache key
  set req.http.x-tokowaka-api-key = "<YOUR API KEY>"; # required for identifying the client
  set req.backend = F_Tokowaka;
}
```

**vcl_hash スニペット**

```
if (req.http.x-tokowaka-config) {
  set req.hash += "tokowaka";
  set req.hash += req.http.x-tokowaka-config;
}
```

**vcl_deliver スニペット**

```
if (req.http.x-tokowaka-config && resp.status >= 400) {
  set req.http.x-tokowaka-request = "failover";
  set req.backend = F_Default_Origin;
  restart;
}

if (!req.http.x-tokowaka-config && req.http.x-tokowaka-request == "failover") {
  set resp.http.x-tokowaka-fo = "1";
}
```

>[!ENDTABS]

>[!NOTE]
>その他の CDN プロバイダーについては、`llmo-at-edge@adobe.com` に連絡して、IT/CDN チームのオンボーディングを支援してください。 設定が完了したら、LLM OptimizerでEdgeのオポチュニティを最適化するための提案をデプロイできます。

## 機会

次の表に、エージェンティック web エクスペリエンスを改善でき、Edgeでの最適化でサポートされているオポチュニティを示します。

| 商談 | タイプ | 自動識別 | 自動提案 | 自動最適化 |
|---------|----------|----------|----------|----------|
| コンテンツの表示の復元 | テクニカル地域 | 重要なコンテンツが非表示になっているページを AI エージェントから検出します。 影響を受ける URL と、復元可能な期待されるコンテンツを表示します。 | AI エージェントに利用可能にできるコンテンツをハイライト表示し、それらのページのプリレンダリングを有効にすることをお勧めします。 | 以前に非表示になっていたコンテンツを復元するエージェンティックトラフィックに、完全にレンダリングされ、AI に対応したHTML スナップショットを提供します。 |
| LLM の見出しの最適化 | コンテンツの最適化 | 見出しをスキャンして、空の見出し、重複する見出し、見つからない見出しまたはあいまいな見出しを検出します。これにより、機械の読みやすさが低下する可能性があります。 | よりクリーンな見出し階層と改善されたラベルを提案し、各ページの更新された構造のプレビューを表示します。 | AI エージェントの改善された見出し構造を挿入し、視覚的なデザインを維持しながら、LLM にとってページをわかりやすくします。 |
| LLM に対応するサマリーの追加 | コンテンツの最適化 | 長いページや複雑なページを特定する際に、ページレベルまたはセクションレベルで簡潔な要約が欠落していると、AI がすばやくスキャンして理解するのが難しくなります。 | では、主要なコンテンツをキャプチャする短い AI で生成された概要をページレベルやセクションレベルで推奨します。 | 関連するHTML セクションに概要を挿入し、モデルがページコンテンツを解釈および記述する方法を改善します。 |
| 関連する FAQ の追加 | コンテンツの最適化 | FAQ のメリットとなる可能性のある、既存のページコンテンツ内の意図的なギャップを検出します。 | ユーザーの意図と既存のトピックに合わせて、AI が生成した FAQ コンテンツを提案します。 | HTMLに FAQ コンテンツを挿入することで、ページを見つけやすくし、AI 主導の回答に関連性を高めます。 |
| 複雑なコンテンツのシンプル化 | コンテンツの最適化 | AI の理解を妨げる可能性のある複雑なテキストを含むページにフラグを付けます。 | は、元の意味を維持しながら、複雑なテキストを AI で生成した簡略版で提供します。 | ページ内の複雑なセクションを書き換え、AI の読みやすさを向上させます。 |

### 追加ツール

[Adobe LLM Optimizer：あなたの Web ページはキャッシュ可能ですか？Chrome拡張機能 &#x200B;](https://chromewebstore.google.com/detail/adobe-llm-optimizer-is-yo/jbjngahjjdgonbeinjlepfamjdmdcbcc) 使用すると、Web ページコンテンツの LLM がアクセスできる量と、非表示のままの内容を正確に確認できます。 無料のスタンドアロン診断ツールとして設計されているため、製品ライセンスやセットアップは必要ありません。

シングルクリックで、任意のサイトの機械読みやすさを評価できます。 AI エージェントが表示する内容と人間のユーザーが表示する内容を横に並べて比較し、LLM Optimizerを使用して復元できるコンテンツの量を推定できます。 [AI があなたのウェブサイトを読むことができるか？](https://business.adobe.com/jp/blog/introducing-the-llm-optimizer-chrome-extension) しくは、ページを参照してください。

## 商談の詳細

以降の節では、Edgeでの最適化でサポートされている各オポチュニティの追加の詳細を確認できます。

### コンテンツの表示の復元

このオポチュニティは、クライアントサイドレンダリングが原因で、AI エージェントに対して主要なコンテンツが非表示になっているページにフラグを付けます。 識別された各ページについて、AI エージェント ビューに表示されないコンテンツを正確に示し、可視性のギャップを強調し、変更を直接適用して非表示のコンテンツを回復できるようにします。 Edgeで最適化を使用してこのオポチュニティをデプロイすると、AI で最適化されたレンダリング済みバージョンのページが LLM ユーザーエージェントに提供されるので、LLM ユーザーエージェントは JavaScript を実行せずにコンテキスト全体にアクセスできます。
これにより、最初にページが AI エージェントに完全に表示されます。 プリレンダリングされたHTMLに加えて、さらに機能強化が行われています。

>[!IMPORTANT]
>このプリレンダリング機能は、Edgeで最適化を使用してデプロイされた場合に、以下に示すすべてのオポチュニティに自動的に適用されます。

### LLM の見出しの最適化

この機会は、見出しの構造により、空、重複、欠落またはあいまいな見出しのために AI エージェントがページを理解するのが難しいページを検出します。 影響を受ける各ページについて、最適でない見出しが表示され、より明確な階層が推奨されます。 Edgeで最適化を使用してデプロイした場合、改善された見出しはHTMLで適用され、エージェンティックトラフィックに提供されます。 これにより、人間が直面するレイアウトはそのままに、機械の読みやすさが向上します。

### LLM に対応するサマリーの追加

このオポチュニティは、LLM がページコンテンツの概要をすばやく理解するのに役立つ、簡潔な要約からメリットを得られるページを特定します。 オポチュニティはページごとに、最も必要な概要を検出し、ページレベルまたはセクションレベルで AI によって生成された概要を作成します。 Edgeで最適化を使用してデプロイすると、これらの要約は AI エージェントが取得するHTMLに挿入され、コンテンツをより正確に記述できる可能性が高くなります。

### 関連する FAQ の追加

このオポチュニティは、追加の Q&amp;A コンテンツが、AI による検出のユーザーの意図とプロンプトにより一致する可能性があるページをフラグ付けします。 ページごとに、ユーザーインテントとページ上のコンテンツに関連付けられた AI によって生成された FAQ ブロックを提案します。 Edgeでの最適化を使用すると、これらの FAQ がHTMLに挿入されるので、ページがより AI に対応し、AI の回答がガイダンスに直接反映される可能性が高まります。

### 複雑なコンテンツのシンプル化

この機会に、AI の理解度を低下させる可能性のある、長く複雑な段落を含むページが見つかります。 読みやすさのしきい値を超えるページごとに、元の意味を維持しながら、より簡単でスキャンしやすい AI 生成コンテンツを作成します。 エッジサイドにデプロイすると、エージェンティックトラフィックに配信される簡略化されたコンテンツは、LLM がコンテンツをより忠実に解釈し、要約するのに役立ちます。

## Edgeでの自動最適化

オポチュニティごとに、エッジで最適化をプレビュー、編集、デプロイ、ライブプレビュー、ロールバックできます。

### プレビュー

**プレビュー** を使用すると、提案が実稼動になる前に影響を確認できます。 これにより、現在のページと提案を適用した後に期待される AI に最適化されたバージョンとの横に並んだ違いが示されます。 このビューでは、ライブトラフィックを強化するのと同じEdgeでの最適化ロジックを使用しますが、分離されたプレビューモードとなります。 これはレビューのための読み取り専用シミュレーションなので、ライブトラフィックには影響しません。

![プレビュー](/help/assets/optimize-at-edge/preview.png)

### 編集

**編集** を使用すると、自動生成された提案をデプロイ前に完全に調整または書き換えることができます。 提案を受け入れる代わりに、編集ワークフローを通じてフルコントロールを維持します。 ビューには、構造化エディターで提案された変更が表示され、元のインテントに合わせてテキストを変更できます。 編集したバージョンは、デプロイ後、AI エージェントに提供されます。

![編集](/help/assets/optimize-at-edge/edit.png)

### デプロイ

**デプロイ** 最適化されたエクスペリエンスをエッジから AI エージェントに提供できるように、選択した提案を公開します。 CDN が完全にルーティングされると、ドメイン内のすべてのページは通常、新しい変更で数分以内に有効になります。 ルーティングが選択パスのみに設定されている場合、許可リストに加えるされたページのみが最適化されて有効になります。

![&#x200B; デプロイ &#x200B;](/help/assets/optimize-at-edge/deploy.png)

### ライブで表示

**ライブで表示** を使用すると、最適化がライブであり、エージェンティックトラフィックに対して期待どおりに動作していることを確認できます。このようなビューは通常、アクセスが困難です。 「固定提案」の下にライブページが表示され、AI エージェントに表示されるようにページがレンダリングされます。

![&#x200B; ライブで表示 &#x200B;](/help/assets/optimize-at-edge/view-live.png)

### ロールバック

ロールバックを実行すると、以前にデプロイした最適化が安全に元に戻ります。 通常、ページの AI 専用バージョンは数分以内に以前の状態に戻るので、必要に応じて最適化を安全に試すことができます。

![&#x200B; ロールバック &#x200B;](/help/assets/optimize-at-edge/rollback.png)

## よくある質問

Q. Edgeの最適化でターゲットにする LLM の種類を教えてください。

ターゲットにするユーザーエージェントのリストは、オンボーディングプロセス中に定義します。

<!--Q. What does "Edge" in Optimize at Edge mean?

In our context, "Edge" means that the optimization is applied at the CDN layer and not inside your CMS.

Q. Why does this optimization require a CDN?

The CDN is where the optimized version of the page is assembled and delivered to AI agents. We leverage the CDN to ensure your origin CMS remains unchanged. This separation lets you improve LLM visibility without altering your existing publishing workflows.-->

Edgeで Optimize にまだオンボーディングしていない場合はどうなりますか？

必要な設定を完了する前に **最適化をデプロイ** をクリックした場合、サイトには何も適用されません。 代わりに、ポップアップダイアログが表示され、オンボーディングサポートを受けるために `llmo-at-edge@adobe.com` のチームに連絡するように求められます。 オンボーディングが完了するまで、検出された商談と提案を参照できますが、ワンクリックデプロイメントワークフローは非アクティブのままになります。

Q：コンテンツがソースで更新されるとどうなりますか？

基になるソースページが変更されていない限り、最適化されたバージョンのページをキャッシュから提供します。 ただし、ソースが変更されると、システムが自動的に更新されるので、AI エージェントは常に最新のコンテンツを受け取ります。 これは、サイト上のコンテンツが更新されると、そのウィンドウ内で新しい最適化がトリガーされるように、低キャッシュの有効期限（TTL）設定を（分順で）使用するからです。 すべてのサイトに適合するユニバーサル TTL はないので、キャッシュ無効化ルールに基づいてこの TTL を設定して、両方のシステムが確実に同期するようにできます。

Edgeでの最適化は、Adobe Edge 配信サービス（EDS）を使用するサイトに対してのみですか？

いいえ。Edgeでの最適化は、CDN に依存せず、Adobeの EDS スタックにデプロイされたものだけでなく、あらゆるフロントエンドアーキテクチャで機能します。

Q. Edgeのプレレンダリングでの最適化は、従来のサーバーサイドレンダリング（SSR）とどのように異なりますか。

どちらも異なる問題を解決し、連携して動作できます。 従来の SSR ではサーバーサイドのコンテンツはレンダリングされますが、ブラウザーに後から読み込まれるコンテンツは含まれません。 「Edgeで最適化」プリレンダリングは、JavaScriptとクライアントサイドのデータが読み込まれた後にページをキャプチャし、CDN エッジで完全にアセンブルされたバージョンを生成します。 SSR は、人間の経験を向上させることに重点を置いており、Edgeでの最適化は、LLM の web エクスペリエンスを向上させます。

