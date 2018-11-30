---
title: Azure Active Directory v2.0 et le protocole OpenID Connect | Microsoft Docs
description: Créez des applications web à l’aide de l’implémentation v2.0 du protocole d’authentification OpenID Connect d’Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 756d00786005fb6de26ff363d4e233fc28b48687
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426840"
---
# <a name="azure-active-directory-v20-and-the-openid-connect-protocol"></a>Azure Active Directory v2.0 et le protocole OpenID Connect

OpenID Connect est un protocole d’authentification basé sur OAuth 2.0 que vous pouvez utiliser pour connecter de façon sécurisée un utilisateur à une application Web. En utilisant l’implémentation du point de terminaison v2.0 d’OpenID Connect, vous pouvez ajouter l’accès à la connexion et aux API à vos applications web. Cet article est indépendant du langage. Il explique comment envoyer et recevoir des messages HTTP sans utiliser l’une des bibliothèques open source de Microsoft.

> [!NOTE]
> Le point de terminaison v2.0 ne prend pas en charge l’intégralité des scénarios et fonctionnalités d’Azure Active Directory (Azure AD). Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limitations de v2.0](active-directory-v2-limitations.md).

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) étend le protocole *d’autorisation* OAuth 2.0 pour l’utiliser en tant que protocole *d’authentification*, ce qui vous permet de procéder à une authentification unique à l’aide d’OAuth. OpenID Connect introduit le concept de *jeton d'ID*, qui est un jeton de sécurité permettant au client de vérifier l’identité de l’utilisateur. Il permet également les informations de base de profil de l'utilisateur. Comme OpenID Connect étend OAuth 2.0, les applications peuvent acquérir de manière sûre les *jetons d’accès* pouvant être utilisés pour accéder à des ressources sécurisées à l’aide d’un [serveur d’autorisation](active-directory-v2-protocols.md#the-basics). Le point de terminaison v2.0 permet également aux applications tierces qui sont inscrites auprès d’Azure AD d’émettre des jetons d’accès pour des ressources sécurisées, telles que des API web. Pour plus d’informations sur la configuration d’une application pour l’émission de jetons d’accès, consultez [Inscription d’une application avec le point de terminaison v2.0](quickstart-v2-register-an-app.md). Nous recommandons d'utiliser OpenID Connect si vous concevez une [application web](v2-app-types.md#web-apps) hébergée sur un serveur et accessible par le biais d’un navigateur.

## <a name="protocol-diagram-sign-in"></a>Schéma de protocole : Connexion

Le flux de connexion le plus simple se compose des étapes présentées dans le diagramme suivant. Chaque étape est décrite en détail dans cet article.

![Protocole OpenID Connect : Connexion](./media/v2-protocols-oidc/convergence_scenarios_webapp.png)

## <a name="fetch-the-openid-connect-metadata-document"></a>Récupérez le document de métadonnées OpenID Connect

OpenID Connect décrit un document de métadonnées qui contient la plupart des informations nécessaires pour qu’une application effectue la connexion. Cela inclut des informations telles que les URL à utiliser et l’emplacement des clés de signature publiques du service. Pour le point de terminaison v2.0, voici le document de métadonnées OpenID Connect que vous utilisez :

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Essayez ! Cliquez sur [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) pour voir la configuration des clients `common`.

`{tenant}` peut prendre une des quatre valeurs :

| Valeur | Description |
| --- | --- |
| `common` |Les utilisateurs avec un compte Microsoft personnel et un compte professionnel/scolaire Azure Active Directory (Azure AD) peuvent se connecter à l’application. |
| `organizations` |Seuls les utilisateurs avec des comptes professionnels ou scolaires Azure AD peuvent se connecter à l’application. |
| `consumers` |Seuls les utilisateurs avec un compte personnel Microsoft peuvent se connecter à l’application. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` |Seuls les utilisateurs avec un compte professionnel ou scolaire d'un client Azure AD spécifique peuvent se connecter à l’application. Le nom de domaine convivial du client Azure AD ou l’identificateur GUID du client peut être utilisé. |

Les métadonnées représentent un simple document JavaScript Objet Notation (JSON). Consultez l’extrait suivant pour obtenir un exemple. Le contenu de l'extrait de code est décrit en détail dans les [spécifications d’OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/common\/discovery\/v2.0\/keys",

  ...

}
```

En règle générale, vous utilisez ce document de métadonnées pour configurer une bibliothèque OpenID Connect ou un Kit de développement logiciel (SDK). La bibliothèque utilise les métadonnées pour faire son travail. Toutefois, si vous n’utilisez pas une bibliothèque OpenID Connect pré-build, vous pouvez suivre les étapes décrites dans le reste de cet article pour vous connecter à une application web à l’aide du point de terminaison v2.0.

## <a name="send-the-sign-in-request"></a>Envoyer la requête de connexion

Lorsque votre application web a besoin d’authentifier l’utilisateur, elle peut le diriger vers le point de terminaison `/authorize`. Cette requête est similaire au premier tronçon du [flux de code d’autorisation OAuth 2.0](v2-oauth2-auth-code-flow.md), avec toutefois ces distinctions importantes :

* La requête doit inclure l’étendue `openid` dans le paramètre `scope`.
* Le paramètre `response_type` doit inclure `id_token`.
* La demande doit inclure le paramètre `nonce` .

> [!IMPORTANT]
> Afin de demander un jeton d’ID correctement, l’inscription de l’application dans le [portail d’inscription](https://apps.dev.microsoft.com) doit avoir **[l’Octroi implicite](v2-oauth2-implicit-grant-flow.md)** activé pour le client web. S’il n’est pas activé, une erreur `unsupported_response` est renvoyée : « la valeur fournie pour le paramètre d’entrée ’response_type’ n’est pas autorisée pour ce client. La valeur attendue est ’code’ »

Par exemple : 

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> Cliquez sur le lien suivant pour exécuter cette requête. Une fois que vous êtes connecté, votre navigateur sera redirigé vers https://localhost/myapp/, avec un jeton d’ID dans la barre d’adresse. Notez que cette requête utilise `response_mode=fragment` (pour les besoins du didacticiel uniquement). Nous vous recommandons d'utiliser `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Paramètre | Condition | Description |
| --- | --- | --- |
| locataire |Obligatoire |Vous pouvez utiliser la valeur `{tenant}` dans le chemin d’accès de la requête pour contrôler les utilisateurs qui peuvent se connecter à l’application. Les valeurs autorisées sont `common`, `organizations`, `consumers` et les identificateurs du client. Pour plus d’informations, consultez les [principes de base du protocole](active-directory-v2-protocols.md#endpoints). |
| client_id |Obligatoire |ID d’application que le [portail d’inscription des applications](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) a affecté à votre application. |
| response_type |Obligatoire |Doit inclure `id_token` pour la connexion à OpenID Connect. Il peut également inclure d’autres valeurs `response_type`, par exemple `code`. |
| redirect_uri |Recommandé |L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| scope |Obligatoire |Une liste d’étendues séparées par des espaces. Pour OpenID Connect, vous devez inclure l’étendue `openid`, qui correspond à l’autorisation de connexion dans l’interface utilisateur de consentement. Vous pouvez inclure d’autres étendues dans cette requête pour solliciter le consentement. |
| nonce |Obligatoire |Une valeur incluse dans la requête, générée par l’application, qui sera intégrée dans la valeur id_token résultant en tant que revendication. L’application peut vérifier cette valeur afin de contrer les attaques par relecture de jetons. La valeur est généralement une valeur unique, aléatoire pouvant être utilisé pour identifier l’origine de la requête. |
| response_mode |Recommandé |Spécifie la méthode à utiliser pour envoyer le code d’autorisation résultant à votre application. Peut être `form_post` ou `fragment`. Pour les applications web, nous vous recommandons d’utiliser `response_mode=form_post` pour garantir le transfert le plus sécurisé des jetons à votre application. |
| state |Recommandé |Une valeur incluse dans la requête qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher les attaques par falsification de requête intersites](https://tools.ietf.org/html/rfc6749#section-10.12). La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| prompt |Facultatif |Indique le type d’interaction utilisateur requis. Les seules valeurs valides pour l’instant sont `login`, `none` et `consent`. La revendication `prompt=login` oblige l'utilisateur à saisir ses informations d'identification lors de cette requête, annulant de fait l'authentification unique. La revendication `prompt=none` a l'effet inverse. Cette valeur garantit qu'aucune invite interactive d'aucune sorte n'est présentée à l'utilisateur. Si la demande ne peut pas être exécutée en mode silencieux au moyen d’une authentification unique, le point de terminaison v2.0 renvoie une erreur. La revendication `prompt=consent` déclenche l'affichage de la boîte de dialogue de consentement OAuth une fois que l’utilisateur se connecte. La boîte de dialogue invite l’utilisateur à accorder des autorisations à l’application. |
| login_hint |Facultatif |Vous pouvez utiliser ce paramètre pour remplir au préalable le champ réservé au nom d’utilisateur et à l’adresse électronique de la page de connexion de l’utilisateur si vous connaissez déjà son nom d’utilisateur. Les applications utilisent souvent ce paramètre au cours de la réauthentification, après avoir extrait le nom d’utilisateur à partir d’une connexion précédente à l’aide de la revendication `preferred_username`. |
| domain_hint |Facultatif |Cette valeur peut être `consumers` ou `organizations`. S’il est inclus, ce paramètre ignore le processus de découverte par courrier électronique auquel l’utilisateur doit se soumettre sur la page de connexion v2.0, ce qui améliore légèrement l’expérience utilisateur. Les applications utilisent souvent ce paramètre au cours de la réauthentification, en extrayant la revendication `tid` du jeton d’ID. Si la valeur de revendication `tid` est `9188040d-6c67-4c5b-b112-36a304b66dad` (le client consommateur de compte Microsoft), utilisez `domain_hint=consumers`. Sinon, utilisez `domain_hint=organizations`. |

À ce stade, l’utilisateur est invité à saisir ses informations d’identification et à exécuter l’authentification. Le point de terminaison v2.0 vérifie que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope` . Si l’utilisateur n’a pas accepté l’une ou plusieurs de ces autorisations, le point de terminaison v2.0 lui demande de corriger ce manquement. Vous pouvez en savoir plus sur les [autorisations consentements et applications mutualisées](v2-permissions-and-consent.md).

Une fois que l’utilisateur a procédé à l’authentification et accordé son consentement, le point de terminaison v2.0 renvoie une réponse à votre application à l'URI de redirection indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

### <a name="successful-response"></a>Réponse correcte

Une réponse correcte lorsque vous utilisez `response_mode=form_post` ressemble à ceci :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paramètre | Description |
| --- | --- |
| id_token |Le jeton d'ID que l’application a demandé. Vous pouvez utiliser le paramètre `id_token` pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. Pour plus d’informations sur les jetons d’ID et leur contenu, consultez les [références `id_tokens`](id-tokens.md). |
| state |Si un paramètre `state` est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

### <a name="error-response"></a>Réponse d’erreur

Les réponses d’erreur peuvent également être envoyées à l'URI de redirection, de manière que l’application puisse les traiter. Une réponse d’erreur ressemble à ceci :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| --- | --- |
| error |Une chaîne de code d’erreur que vous pouvez utiliser pour classer les types d’erreur se produisant et pour intervenir face aux erreurs. |
| error_description |Un message d’erreur spécifique qui peut vous aider à identifier la cause principale d’une erreur d’authentification. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Codes d’erreur pour les erreurs de point de terminaison d’autorisation

Le tableau suivant décrit les codes d’erreur qui peuvent être retournés dans le paramètre `error` de la réponse d’erreur :

| Code d'erreur | Description | Action du client |
| --- | --- | --- |
| invalid_request |Erreur de protocole, tel qu’un paramètre obligatoire manquant. |Corrigez l’erreur, puis envoyez à nouveau la demande. Il s’agit d’une erreur de développement généralement détectée lors des tests initiaux. |
| unauthorized_client |L’application cliente ne peut pas demander un code d’autorisation. |Cela se produit généralement lorsque l’application cliente n’est pas inscrite dans Azure AD ou n’est pas ajoutée au client Azure AD de l’utilisateur. L’application peut proposer à l’utilisateur des instructions pour installer l’application et l’ajouter à Azure AD. |
| access_denied |Le propriétaire de la ressource n’a pas accordé son consentement. |L’application cliente peut avertir l’utilisateur qu’elle ne peut pas continuer sans son consentement. |
| unsupported_response_type |Le serveur d’autorisation ne prend pas en charge le type de réponse dans la demande. |Corrigez l’erreur, puis envoyez à nouveau la demande. Il s’agit d’une erreur de développement généralement détectée lors des tests initiaux. |
| server_error |Le serveur a rencontré une erreur inattendue. |relancez la requête. Ces erreurs peuvent résulter de conditions temporaires. L’application cliente peut expliquer à l’utilisateur que sa réponse est reportée en raison d’une erreur temporaire. |
| temporarily_unavailable |Le serveur est temporairement trop occupé pour traiter la demande. |relancez la requête. L’application cliente peut expliquer à l’utilisateur que sa réponse est reportée en raison d’une condition temporaire. |
| invalid_resource |La ressource cible n’est pas valide car elle n’existe pas, Azure AD ne la trouve pas ou elle n’est pas configurée correctement. |Cela indique que la ressource, si elle existe, n’a pas été configurée dans le client. L’application peut proposer à l’utilisateur des instructions pour installer l’application et l’ajouter à Azure AD. |

## <a name="validate-the-id-token"></a>Validation du jeton d’ID

La réception du jeton id_token ne suffit pas à authentifier l’utilisateur. Vous devez valider la signature du jeton id_token et vérifier la conformité des revendications du jeton par rapport à la configuration requise de votre application. Le point de terminaison v2.0 utilise les [jetons web JSON (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et le chiffrement de clés publiques pour signer les jetons et vérifier leur validité.

Vous pouvez décider de valider l’élément `id_token` dans le code du client, mais une pratique courante consiste à envoyer l’élément `id_token` vers un serveur principal, afin d’y appliquer la validation. Une fois que vous avez validé la signature du jeton id_token, il vous faudra vérifier quelques revendications. Pour plus d’informations, consultez la page de [référence `id_token`](id-tokens.md), notamment les sections [Validation des jetons](id-tokens.md#validating-an-idtoken) et [Informations importantes sur la substitution des clés de signature](active-directory-signing-key-rollover.md). Nous vous recommandons d’utiliser une bibliothèque pour analyser et valider les jetons. Il en existe au moins une pour la plupart des langages et plateformes.
<!--TODO: Improve the information on this-->

En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires. Voici quelques validations courantes :

* S’assurer que l’utilisateur/l’organisation s’est inscrit pour l’application.
* S’assurer que l’utilisateur dispose de l’autorisation/des privilèges appropriés.
* S’assurer de l’utilisation d’une force certaine d’authentification, comme une authentification multifacteur.

Une fois que vous avez complètement validé le jeton id_token, vous pouvez démarrer une session avec l’utilisateur et utiliser les revendications du jeton id_token pour récupérer les informations sur l’utilisateur dans votre application. Ces informations peuvent être utilisées pour l’affichage, les enregistrements, la personnalisation, etc.

## <a name="send-a-sign-out-request"></a>Envoi d’une demande de déconnexion

Si vous souhaitez déconnecter l’utilisateur de votre application, vous ne pouvez pas vous contenter de supprimer les cookies de votre application ou d’arrêter la session de l’utilisateur. Vous devez également rediriger l’utilisateur vers le point de terminaison v2.0 pour suivre la procédure de déconnexion. Si vous ne le faites pas, l’utilisateur pourra se réauthentifier auprès de votre application sans avoir à saisir de nouveau ses informations d’identification, puisqu’il disposera d’une session d’authentification unique valide avec le point de terminaison v2.0.

Vous pouvez rediriger l’utilisateur vers le `end_session_endpoint` répertorié dans le document de métadonnées OpenID Connect :

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Paramètre | Condition | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | Recommandé | URL vers laquelle l’utilisateur est redirigé après sa déconnexion. Si le paramètre n’est pas inclus, un message générique généré par le point de terminaison v2.0 s’affiche. Cette URL doit correspondre exactement à l’un des URI de redirection inscrits pour votre application dans le portail d’inscription des applications. |

## <a name="single-sign-out"></a>Authentification unique

Lorsque vous redirigez l’utilisateur vers `end_session_endpoint`, le point de terminaison v2.0 efface la session de l’utilisateur dans le navigateur. Toutefois, l’utilisateur peut rester connecté à d’autres applications qui utilisent des comptes Microsoft pour s’authentifier. Pour permettre à ces applications de déconnecter simultanément l’utilisateur, le point de terminaison v2.0 envoie une requête HTTP GET au paramètre `LogoutUrl` inscrit de toutes les applications auxquelles l’utilisateur est actuellement connecté. Les applications doivent répondre à cette requête en effaçant toute session qui identifie l’utilisateur et en renvoyant une réponse `200`. Si vous souhaitez prendre en charge la déconnexion unique dans votre application, vous devez implémenter ce paramètre `LogoutUrl` dans le code de votre application. Vous pouvez définir le paramètre `LogoutUrl` à partir du portail d’inscription des applications.

## <a name="protocol-diagram-access-token-acquisition"></a>Schéma de protocole : Acquisition de jeton d’accès

Beaucoup d’applications web nécessitent une connexion de l’utilisateur, puis un accès au service web pour le compte de cet utilisateur à l’aide d’OAuth. Ce scénario utilise OpenID Connect pour l’authentification de l’utilisateur tout en récupérant un code d’autorisation vous permettant d'obtenir des jetons d’accès si vous utilisez le flux de code d’autorisation OAuth.

Le flux complet de connexion OpenID Connect et d’acquisition des jetons ressemble à l'exemple du diagramme suivant. Nous décrirons en détail chaque étape dans les sections suivantes de cet article.

![Protocole OpenID Connect : Acquisition de jeton](./media/v2-protocols-oidc/convergence_scenarios_webapp_webapi.png)

## <a name="get-access-tokens"></a>Obtenir des jetons d’accès
Pour acquérir des jetons d’accès, modifiez la requête de connexion :

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Cliquez sur le lien suivant pour exécuter cette requête. Une fois que vous êtes connecté, votre navigateur est redirigé vers https://localhost/myapp/, avec un jeton d’ID et un code dans la barre d’adresse. Notez que cette requête utilise `response_mode=fragment` (pour les besoins du didacticiel uniquement). Nous vous recommandons d'utiliser `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

En incluant des étendues d’autorisation dans la requête et en utilisant `response_type=id_token code`, le point de terminaison v2.0 garantit que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope`. Il renvoie un code d’autorisation à votre application afin de l’échanger contre un jeton d’accès.

### <a name="successful-response"></a>Réponse correcte

Une réponse correcte utilisant `response_mode=form_post` se présente ainsi :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Paramètre | Description |
| --- | --- |
| id_token |Le jeton d'ID que l’application a demandé. Vous pouvez utiliser le jeton d'ID pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. Pour plus de détails sur les jetons d'ID et leur contenu, consultez la page de [référence `id_tokens`](id-tokens.md). |
| code |Le code d’autorisation demandé par l’application. L’application peut utiliser ce code d’autorisation pour demander un jeton d’accès pour la ressource cible. La durée de vie d'un code d’autorisation est très courte. En règle générale, un code d’autorisation expire au bout de 10 minutes environ. |
| state |Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

### <a name="error-response"></a>Réponse d’erreur

Les réponses d’erreur peuvent également être envoyées à l'URI de redirection, de manière que l’application puisse les traiter de manière appropriée. Une réponse d’erreur ressemble à ceci :

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| --- | --- |
| error |Une chaîne de code d’erreur que vous pouvez utiliser pour classer les types d’erreur se produisant et pour intervenir face aux erreurs. |
| error_description |Un message d’erreur spécifique qui peut vous aider à identifier la cause principale d’une erreur d’authentification. |

Pour obtenir une description des codes d’erreur éventuels et connaître les réponses client recommandées associées, consultez [Codes d’erreur pour les erreurs de point de terminaison d’autorisation](#error-codes-for-authorization-endpoint-errors).

Une fois que vous avez obtenu un code d'autorisation et un jeton d'ID, vous pouvez connecter l’utilisateur et obtenir des jetons d’accès pour son compte. Pour connecter l’utilisateur, vous devez valider le jeton d'ID [exactement comme décrit](id-tokens.md#validating-an-idtoken). Pour obtenir des jetons d’accès, suivez la procédure décrite dans la [documentation du flux de code OAuth](v2-oauth2-auth-code-flow.md#request-an-access-token).
