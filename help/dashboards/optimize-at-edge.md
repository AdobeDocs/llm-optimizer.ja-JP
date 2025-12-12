---
title: Edgeで最適化
description: オーサリングを変更せずに、CDN エッジでLLM Optimizerで最適化を配信する方法を説明します。
feature: Opportunities
source-git-commit: 3c6f287b3c3787cee95f99b7031412f26692a88b
workflow-type: tm+mt
source-wordcount: '2291'
ht-degree: 1%

---


# Edgeで最適化

このセクション…

## Edgeでの最適化とは

Edgeでの最適化は、LLM Optimizerのエッジベースのデプロイメント機能であり、LLM ユーザーエージェントに対して AI に対応した変更を提供できます。 CDN エッジで最適化が行われるので、Content Management System （CMS）でのオーサリング変更は必要ありません。 また、エージェンティックトラフィックのみをターゲットにし、人間のユーザーや SEO ボットには影響しません。

LLM Optimizerがページを最適化する機会を検出すると、プラットフォームを変更することなく、エッジに直接フィックスをデプロイできます。

この機能は現在、早期アクセス中です。

## 顧客が関心を持つ理由

Edgeでの最適化は、複雑なエンジニアリング作業を必要とする従来の修正に代わる、より迅速で無駄のない方法です。 お客様が 1 回限りの設定を完了すると、web ページに変更を適用するために、プラットフォームを変更したり、開発サイクルを長く繰り返す必要はなくなります。 ユーザーは、開発者のエンゲージメントを必要とせずに、改善点を数週間ではなく数分で公開できます。 これは、AI エージェント用に web サイトを最適化するための、リスクの低いコードなしの方法です。

### 主なメリットと価値提案

* **AI のみの配信：** 最適化されたHTMLを AI エージェントにのみ提供し、人間の訪問者や SEO ボットには影響しません。
* **サイクルの高速化：** 変更を数週間ではなく数分で公開します。 プラットフォームの変更や長いエンジニアリングサイクルは不要です。
* **低リスクで可逆的：** ページを数分で元に戻せるワンクリックロールバック機能がサポートされています。
* **パフォーマンスへの影響なし：** Edge ベースの最適化とキャッシュにより、サイトの待ち時間に影響しません。
* **CDN とCMSに依存しない：** CMSに関係なく、任意の CDN 設定およびフロントエンド設定と連携します。

## 誰が使用しますか？

Edgeでの最適化は、マーケティング、SEO、コンテンツ、デジタル戦略の各チームのビジネスユーザー向けに設計されています。 ビジネスユーザーはLLM Optimizerで、オポチュニティの特定、提案の理解、簡単な修正点のデプロイなどのすべてのジャーニーを完了できます。 Edgeでの最適化を使用すると、変更をプレビューし、エッジですばやくデプロイして、最適化がライブであることを検証できます。 パフォーマンスは、LLM Optimizerのエコシステムでトラッキングできます。

## Edgeで最適化が行われているオポチュニティはどれですか。

Edgeの最適化では、効率的な web エクスペリエンスを実現するオポチュニティがサポートされています。 各商談について詳しくは、「商談 [&#x200B; の節を参照し &#x200B;](/help/dashboards/opportunities.md) ください。

## オンボーディング

LLM Optimizerにオンボーディングし、CDN ログを転送している場合は、「Edgeでの最適化」を有効にできます。

Edgeで最適化を有効にするには、CDN エンジニアが初期設定を行う必要があります。

設定の要件：

* API キーを生成します。
* CDN のEdge ルーティングルールで最適化を追加します。
* ユーザー定義のパスまたはドメイン全体を許可リストに加えるします。
* ユーザー定義の LLM ユーザー・エージェント・リストをターゲットに追加します。
* robots.txt が、ターゲットとするユーザーエージェントをブロックしないようにします。
* LLM Optimizer UI でEdge ルーティングで最適化を選択します。

Adobeは、設定プロセスをガイドするために、ほとんどの主要な CDN に対してサンプルの設定スニペットを提供します。 ガイドラインに含まれるスニペットの例は、実際のライブ設定に合わせて変更する必要があります。 Adobeでは、最初に下位環境に変更を実装することをお勧めします。

>[!BEGINTABS]

>[!TAB AEM Cloud Service の管理による CDN （Fastly） ]

**Tokowaka BYOCDN - Adobe Managed CDN**

originSelectors のみを使用して、Tokowaka オリジンを選択します。

次の例では、「/es/*」または正確なパスのパターンに一致する特定のドメインで LLM エージェントリクエストをルーティングします（HTML ページのみがルーティングされます）。 この例は、出発点を提供することを想定しています。設定に複数の originSelectors がある場合は、これを最初に配置することをお勧めします。

重要なメモ：

* x-tokowaka-request は、Tokowaka バックエンドにルーティングする前にチェックする必要があります。 このヘッダーを持たないリクエストのみを Tokowaka バックエンドにルーティングする必要があります。
* 複数のルールがある場合、Tokowaka バックエンドにルーティングする originSelector ルールを最初にリストに含める必要があります。
* cdn.yaml をデプロイする前に、TOKOWAKA_API_KEY シークレットをデプロイする必要があります

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

>[!TAB Akamai （BYOCDN） ]

**床若 BYOCDN – 赤舞**

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

重要な考慮事項：

* Tokowaka ルールは User-Agent + Path + x-tokowaka-request （存在しない場合） + TOKOWAKA_DISABLE 変数（1 つの変数トグルを使用してオフを切り替える場合）に基づいてオンになります
* ルールを設定して **受信リクエストヘッダーを変更** ルールを設定してカスタムヘッダーを設定
* キャッシュ ID 変更メカニズムを通じてユーザー定義の変数を使用して、Akamai でキャッシュキーを設定します。 ユーザー定義の変数は 1 つしか使用できないので、cache_key に対して別の変数を作成し、それに応じて設定します。
* Lang: &quot;regex&quot;: &quot;^（[a-zA-Z]{2}）.*&quot;を使用して Accept-Language ヘッダーから抽出されます
* ユーザーエージェントでの一致内のキャッシュ ID 変更を使用すると、URL でコンテンツをパージできません（単に参考まで）
* サイトフェイルオーバーメカニズム：User-Agent ルールの一致では、Akamai は、ヘルスチェックに基づくフェイルオーバーを許可せず、リクエストごとのオリジン応答/接続にのみ基づきます。 フェイルオーバー応答の場合は、**x-tokowaka-fo:true** resp ヘッダーを設定します。
* SWR は Akamai ではサポートされていません。 したがって、TTL ベースのキャッシュのみが存在します。 そのため、Akamai でルールを設定して、オリジン応答から年齢ヘッダーを削除します。削除しない場合、TTL ベースのキャッシュは機能しません。
* ルール階層の最下部に常若ルールを配置する（他のルールを上書きする）。

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


その他の CDN プロバイダーについては、llmo-at-edge@adobe.comに問い合わせて、IT/CDN チームのオンボーディングを支援してください。

<!--This should probably be included Opportunities dashboard content. Content also needs serious editing - lots of "customer needs"and business user" etc.-->

設定が完了したら、ビジネスユーザーはLLM OptimizerでEdgeのオポチュニティを最適化するための提案をデプロイできます。

## 機会

| 商談 | タイプ | 自動識別 | 自動提案 | 自動最適化 |
|---------|----------|----------|----------|----------|
| コンテンツの表示の復元 | テクニカル地域 | 重要なコンテンツが非表示になっているページを AI エージェントから検出します。 影響を受ける URL と、復元可能な期待されるコンテンツを表示します。 | AI エージェントに利用可能にできるコンテンツをハイライト表示し、それらのページのプリレンダリングを有効にすることをお勧めします。 | 以前に非表示になっていたコンテンツを復元するエージェンティックトラフィックに、完全にレンダリングされ、AI に対応したHTML スナップショットを提供します。 |
| AI の見出しの最適化 | コンテンツの最適化 | 見出しをスキャンして、空の見出し、重複する見出し、見つからない見出し、あいまいな見出しを検出します。これにより、機械での読みやすさが低下する可能性があります。 | よりクリーンな見出し階層と改善されたラベルを提案し、各ページの更新された構造のプレビューを表示します。 | AI エージェントの改善された見出し構造を挿入し、視覚的なデザインを維持しながら、LLM にとってページをわかりやすくします。 |
| AI に対応した概要の追加 | コンテンツの最適化 | 長いページや複雑なページを特定する際に、ページレベルまたはセクションレベルで簡潔な要約が欠落していると、AI がすばやくスキャンして理解するのが難しくなります。 | では、主要なコンテンツを取り込んだページレベルとセクションレベルで、AI によって生成された短い要約を使用することをお勧めします。 | 関連するHTML セクションに概要を挿入し、モデルがページコンテンツを解釈および記述する方法を改善します。 |
| 関連する FAQ の追加 | コンテンツの最適化 | FAQ のメリットとなる可能性のある、既存のページコンテンツ内の意図的なギャップを検出します。 | ユーザーの意図や既存のトピックに合わせて、AI が生成した FAQ コンテンツを提案します。 | HTMLに FAQ コンテンツを挿入することで、ページを見つけやすくし、AI 主導の回答に関連性を高めます。 |
| 複雑なコンテンツのシンプル化 | コンテンツの最適化 | AI の理解を妨げる可能性のある複雑なテキストを含むページにフラグを付けます。 | 元の意味を維持しながら、複雑なテストを AI で生成して簡略化します。 | ページ内の複雑なセクションを書き換え、AI の読みやすさを向上させます。 |

### コンテンツの表示の復元

このオポチュニティは、クライアントサイドレンダリングが原因で、AI エージェントに対して主要なコンテンツが非表示になっているページにフラグを付けます。 識別された各ページについて、AI エージェント ビューに表示されないコンテンツを正確に示し、可視性のギャップを強調し、変更を直接適用して非表示のコンテンツを回復できるようにします。 Edgeで最適化を使用してこのオポチュニティをデプロイすると、AI で最適化されたレンダリング済みバージョンのページが LLM ユーザーエージェントに提供されるので、LLM ユーザーエージェントは JavaScript を実行せずにコンテキスト全体にアクセスできます。

**このプリレンダリング機能は、Edgeでの最適化を使用してデプロイされた場合に従うすべてのオポチュニティに自動的に適用されます。** これにより、最初にページが AI エージェントに完全に表示されるようにします。 プリレンダリングされたHTMLに加えて、さらに機能強化が行われています。

#### 追加ツール

あなたのウェブページは可能ですか？ [Adobe LLM Optimizer：あなたの Web ページはキャッシュ可能ですか？Chrome拡張機能 &#x200B;](https://chromewebstore.google.com/detail/adobe-llm-optimizer-is-yo/jbjngahjjdgonbeinjlepfamjdmdcbcc) 使用すると、Web ページコンテンツの LLM がアクセスできる量と、非表示のままの内容を正確に確認できます。 無料のスタンドアロン診断ツールとして設計されているため、製品ライセンスやセットアップは必要ありません。

1 回のクリックで、任意のサイトの機械可読性を評価し、AI エージェントが表示する内容と人間が表示する内容を横に並べて比較し、LLM Optimizerを使用して復元できるコンテンツの量を見積もることができます。 [AI はあなたのウェブサイトを読むことができますか？](https://business.adobe.com/blog/introducing-the-llm-optimizer-chrome-extension) を参照してください。

### LLM の見出しの最適化

この機会は、見出しの構造により、空、重複、欠落またはあいまいな見出しのために AI エージェントがページを理解するのが難しいページを検出します。 影響を受ける各ページについて、最適でない見出しが表示され、より明確な階層が推奨されます。 Edgeで最適化を使用してデプロイすると、改善された見出しは、HTMLがエージェンティックトラフィックに提供されて適用され、人間に向けたレイアウトを変えずに機械の読みやすさを向上させることができます。

### LLM に対応するサマリーの追加

このオポチュニティは、LLM がページの概要をすばやく理解するのに役立つ、簡潔な要約からメリットを得られるページを特定します。 オポチュニティはページごとに、最も必要な概要を検出し、ページレベルやセクションレベルで AI によって生成された概要を作成します。 Edgeで最適化を使用してデプロイすると、これらの要約は AI エージェントが取得するHTMLに挿入され、コンテンツをより正確に記述できる可能性が高くなります。

### 関連する FAQ の追加

このオポチュニティは、追加の Q&amp;A コンテンツが、AI による検出のユーザーの意図とプロンプトにより一致する可能性があるページをフラグ付けします。 ページごとに、ユーザーインテントとページ上のコンテンツに関連付けられた AI によって生成された FAQ ブロックを提案します。 Edgeでの最適化を使用すると、これらの FAQ がHTMLに挿入されるので、ページがより AI に対応し、AI の回答がガイダンスに直接反映される可能性が高まります。

### 複雑なコンテンツのシンプル化

この機会に、AI の理解度を低下させる可能性のある、長く複雑な段落を含むページが見つかります。 読みやすさのしきい値を超えるページごとに、元の意味を維持しながら、よりシンプルでスキャンしやすい AI 生成コンテンツを作成します。 エッジサイドにデプロイすると、エージェンティックトラフィックに配信される簡略化されたコンテンツは、LLM がコンテンツをより忠実に解釈し、要約するのに役立ちます。

## 候補

オポチュニティごとに、エッジで最適化をプレビュー、編集、デプロイ、ライブプレビュー、ロールバックできます。

### プレビュー

プレビューを使用すると、ユーザーは運用開始前に、提案がページに与える影響を確認できます。 これにより、現在のページと提案を適用した後に期待される AI に最適化されたバージョンとの横に並んだ違いが示されます。 このビューでは、ライブトラフィックを強化するのと同じEdgeでの最適化ロジックを使用しますが、安全な分離されたプレビューモードとなります。 これはレビューのための読み取り専用シミュレーションなので、ライブトラフィックには影響しません。

![プレビュー](/help/assets/optimize-at-edge/preview.png)

### 編集

編集を使用すると、自動生成された提案をデプロイする前に完全に調整または書き換えることができます。 ユーザーは、提案を受動的に受け入れるのではなく、このループ内の人間のワークフローを通じて完全なコントロールを維持します。 ビューでは、構造化エディターで変更案が表示され、ユーザーは意図に合わせてテキストを変更できます。 編集したバージョンは、デプロイ後、AI エージェントに提供されます。

![編集](/help/assets/optimize-at-edge/edit.png)

### デプロイ

デプロイでは、最適化されたエクスペリエンスをエッジから AI エージェントに提供できるように、選択した提案を公開します。 CDN が完全にルーティングされると、ドメイン内のすべてのページは、通常、数分以内に新しい変更で有効になります。 ルーティングが選択パスのみに設定されている場合、許可リストに加えるされたページのみが最適化されて有効になります。

![&#x200B; デプロイ &#x200B;](/help/assets/optimize-at-edge/deploy.png)

### ライブで表示

ライブで表示を使用すると、最適化がライブであり、エージェンティックトラフィックに対して期待どおりに動作していることを確認できます。このような表示は通常、アクセスが困難です。 ユーザーは、「固定提案」の下でライブページを表示できます。これにより、AI エージェントに表示されるようにページがレンダリングされます。

![&#x200B; ライブで表示 &#x200B;](/help/assets/optimize-at-edge/view-live.png)

### ロールバック

ロールバックを実行すると、以前にデプロイした最適化が安全に元に戻ります。 通常、ページの AI 専用バージョンは数分以内に以前の状態に戻るので、必要に応じて最適化を試すことができます。

![&#x200B; ロールバック &#x200B;](/help/assets/optimize-at-edge/rollback.png)

## よくある質問

Q. Edgeの最適化でターゲットにする LLM の種類を教えてください。

ターゲットとするユーザーエージェントのリストは、オンボーディング時に顧客によって完全に定義されます。

Q. Edgeでの最適化の「Edge」はどういう意味ですか。

このコンテキストでは、「Edge」とは、最適化がCMS内ではなく CDN レイヤーで適用されることを意味します。

質問：この最適化に CDN が必要なのはなぜですか？

CDN で、ページの最適化されたバージョンが組み立てられ、AI エージェントに配信されます。 CDN を利用することで、接触チャネルのCMSを変更する必要がなくなります。 この分離により、既存の公開ワークフローを変更することなく、LLM の可視性を向上させることができます。

Edgeで Optimize にまだオンボーディングしていない場合はどうなりますか？

必要な設定を完了する前に **最適化をデプロイ** をクリックした場合、サイトには何も適用されません。 代わりに、ポップアップダイアログが表示され、llmo-at-edge@adobe.comでチームに連絡してオンボーディングサポートを受けるように求められます。 オンボーディングが完了するまで、検出された商談と提案を参照できますが、ワンクリックデプロイメントワークフローは非アクティブのままになります。

Q：コンテンツがソースで更新されるとどうなりますか？

基になるソースページが変更されていない限り、最適化されたバージョンのページをキャッシュから提供します。 ただし、ソースが変更されると、システムが自動的に更新されるので、AI エージェントは常に最新のコンテンツを受け取ります。 これは、低キャッシュ TTL を分単位で使用するので、サイト上のコンテンツの更新によって、そのウィンドウ内に新しい最適化がトリガーされるからです。 すべてのサイトに適合するユニバーサル TTL はないので、キャッシュ無効化ルールに基づいてこの TTL を設定して、両方のシステムが確実に同期するようにできます。

Edgeでの最適化は、Adobe Edge 配信サービス（EDS）を使用するサイトに対してのみですか？

いいえ。Edgeでの最適化は、CDN に依存せず、Adobeの EDS スタックにデプロイされたものだけでなく、あらゆるフロントエンドアーキテクチャで機能します。

Q. Edgeのプレレンダリングでの最適化は、従来のサーバーサイドレンダリング（SSR）とどのように異なりますか。

2 つは異なる問題を解決し、連携して動作することができます。 従来の SSR ではサーバーサイドのコンテンツはレンダリングされますが、ブラウザーに後から読み込まれるコンテンツは含まれません。 「Edgeで最適化」プリレンダリングは、JavaScriptとクライアントサイドのデータが読み込まれた後のページをキャプチャし、CDN エッジで完全にアセンブルされたバージョンを生成します。 SSR は、人間の経験を向上させることに重点を置いており、Edgeでの最適化は、LLM の web エクスペリエンスを向上させます。


















