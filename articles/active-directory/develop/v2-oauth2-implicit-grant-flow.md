---
title: Flux d’octroi implicite OAuth 2.0 - Plateforme d’identités Microsoft | Azure
description: Sécurisez les applications à page unique avec le flux implicite de la plateforme d’identités Microsoft.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: fbe74b62352babf7a1fdd93bf19a6e1475e3f032
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85553581"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Plateforme d’identités Microsoft et flux d’octroi implicite

Le point de terminaison de la plateforme d’identités Microsoft vous permet de connecter des utilisateurs dans vos applications à page unique avec des comptes personnels et professionnels ou scolaires de Microsoft. Les applications à page unique et autres applications JavaScript qui s’exécutent principalement dans un navigateur présentent des problématiques spécifiques liées à l’authentification :

* Les caractéristiques de sécurité de ces applications sont considérablement différentes de celles des applications web traditionnelles basées sur serveur.
* De nombreux serveurs d’autorisation et fournisseurs d’identité ne prennent pas en charge les demandes CORS.
* Chacune des redirections à partir de l’application du navigateur plein écran perturbe de manière assez importante l’expérience utilisateur.

Pour ces applications (Angular, Ember.js, React.js, etc.), la plateforme d’identités Microsoft prend en charge le flux d’octroi implicite OAuth 2.0. Le flux implicite est décrit dans la [spécification OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). Il permet notamment à l’application d’obtenir des jetons de la plateforme d’identités Microsoft sans échanger les informations d’identification du serveur principal. L’application peut alors connecter l’utilisateur, maintenir la session et obtenir des jetons pour d’autres API web, le tout dans le code JavaScript client. Quelques points de sécurité importants sont à prendre en compte lorsque vous utilisez le flux implicite, particulièrement en ce qui concerne l’emprunt d’identité du [client](https://tools.ietf.org/html/rfc6749#section-10.3) et de [l’utilisateur](https://tools.ietf.org/html/rfc6749#section-10.3).

Cet article explique comment programmer directement par rapport au protocole dans votre application.  Dans la mesure du possible, nous vous recommandons d’utiliser les bibliothèques d’authentification Microsoft (MSAL) prises en charge au lieu d’[acquérir des jetons et d’appeler des API web sécurisées](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Jetez également un coup d’œil aux [exemples d’applications qui utilisent MSAL](sample-v2-code.md).

Toutefois, si vous préférez ne pas utiliser de bibliothèque dans votre application à page unique et envoyer vous-même des messages de protocole, suivez la procédure générale ci-dessous.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Scénarios adaptés à l’octroi implicite OAuth2

La spécification OAuth2 indique que l’octroi implicite a été conçu pour autoriser les applications d’agent utilisateur, c’est-à-dire les applications JavaScript qui s’exécutent dans un navigateur. La caractéristique déterminante de ces applications réside dans le fait que le code JavaScript est utilisé pour accéder aux ressources du serveur (généralement une API web) et mettre à jour l’expérience utilisateur de l’application en conséquence. Pensez à des applications comme Gmail ou Outlook Web Access : lorsque vous sélectionnez un message dans votre boîte de réception, seul le volet de visualisation du message change pour afficher la nouvelle sélection, tandis que le reste de la page reste inchangé. Cette caractéristique contraste avec les applications web classiques à redirection, pour lesquelles chaque interaction utilisateur entraîne une publication (postback) et un rendu complets de la page de la nouvelle réponse du serveur.

Les applications qui adoptent une approche JavaScript extrême sont appelées des applications monopage ou SPA (single-page applications). En d’autres termes, ces applications diffusent uniquement une page HTML initiale et le code JavaScript associé, et toutes les interactions suivantes sont pilotées par des appels d’API Web effectués via JavaScript. Toutefois, les approches hybrides, où l’application est principalement basée sur la publication mais effectue des appels JS occasionnels, ne sont pas rares. La discussion sur l’utilisation des flux implicites est également pertinente pour ces approches.

En général, les applications à redirection sécurisent leurs demandes par des cookies. Mais cette approche présente une efficacité limitée pour les applications JavaScript. Les cookies ne fonctionnent que sur le domaine pour lequel ils ont été générés, tandis que les appels JavaScript peuvent être dirigés vers d’autres domaines. En fait, ce sera très souvent le cas : pensez aux applications appelant une API Graph Microsoft, une API Office et une API Azure qui, toutes, résident hors du domaine à partir duquel l’application est exécutée. Une tendance croissante pour les applications JavaScript consiste à n’avoir aucun principal et à se reposer à 100 % sur des API web tierces pour implémenter la fonction métier.

Actuellement, la méthode recommandée de protection des appels d’une API Web consiste à utiliser l’approche de jeton de support OAuth2, dans laquelle chaque appel est accompagné d’un jeton d’accès OAuth2. L’API Web examine le jeton d’accès entrant et, si elle y détecte les périmètres nécessaires, elle autorise l’accès à l’opération demandée. Le flux implicite fournit un mécanisme pratique permettant aux applications JavaScript d’obtenir des jetons d’accès pour une API Web, offrant ainsi de nombreux avantages sur les cookies :

* Les jetons peuvent être obtenus de façon fiable sans avoir recours aux appels cross-origin – l’inscription obligatoire de l’URI de redirection vers laquelle les jetons sont retournés garantit que les jetons ne sont pas déplacés.
* Les applications JavaScript peuvent obtenir autant de jetons d’accès que nécessaire, pour un nombre illimité d’API Web ciblées, sans aucune restriction sur les domaines
* Les fonctionnalités HTML5 comme le stockage de session ou local accordent un contrôle total sur la gestion de la mise en cache et de la durée de vie des jetons, tandis que la gestion des cookies est opaque pour l’application.
* Les jetons d'accès ne sont pas vulnérables aux falsifications de requête intersites (CSRF, Cross Site Request Forgery).

Le flux d’octroi implicite n’émet pas de jetons d’actualisation, principalement pour des raisons de sécurité. L'étendue d'un jeton d'actualisation n'est pas aussi étroite que celle des jetons d'accès. Ainsi, un jeton d'actualisation offre une puissance bien supérieure et peut causer des dommages beaucoup plus lourds en cas de fuite. Dans le flux implicite, les jetons sont remis à l’URL. Par conséquent, le risque d’interception est plus élevé qu’avec l’octroi de code d’autorisation.

Toutefois, une application JavaScript a un autre mécanisme à sa disposition pour renouveler les jetons d’accès sans inviter à chaque fois l’utilisateur à entrer ses informations d’identification. L’application peut utiliser un IFrame masqué pour effectuer de nouvelles demandes de jeton sur le point de terminaison d’autorisation d’Azure AD : tant que le navigateur a une session active (c’est-à-dire qu’il dispose d’un cookie de session) sur le domaine Azure AD, la demande d’authentification peut s’effectuer correctement sans aucune intervention de l’utilisateur.

Ce modèle permet à l’application JavaScript de renouveler les jetons d’accès en toute autonomie, voire d’en acquérir d’autres pour une nouvelle API (à condition que l’utilisateur les ait préalablement acceptés). Cela évite les tâches d’acquisition, de maintenance et de protection d’un artefact aussi précieux qu’un jeton d’actualisation. L’artefact qui permet un renouvellement silencieux, le cookie de session Azure AD, est géré en dehors de l’application. Autre avantage de cette approche : un utilisateur peut se déconnecter d’Azure AD, à l’aide des applications signées dans Azure AD et en cours d’exécution dans les onglets de navigateur. Cela entraîne la suppression du cookie de session Azure AD, l’application JavaScript perdant automatiquement la capacité de renouveler les jetons pour l’utilisateur déconnecté.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>L’octroi implicite est-il adapté à mon application ?

L’octroi implicite présente plus de risques que d’autres octrois, et les points auxquels vous devez être attentif sont bien documentés (par exemple, [Mauvaise utilisation du jeton d’accès pour emprunter l’identité du propriétaire des ressources dans un flux implicite][OAuth2-Spec-Implicit-Misuse] et[Modèle de menace et considérations de sécurité OAuth 2.0][OAuth2-Threat-Model-And-Security-Implications]). Toutefois, le profil de risque supérieur est principalement dû au fait qu’il est destiné à autoriser les applications qui exécutent du code actif, envoyé à un navigateur par une ressource distante. Si vous optez pour une architecture d’application à page unique, que vous n’avez aucun composant principal ou que vous envisagez d’appeler une API Web à l’aide de JavaScript, l’utilisation du flux implicite pour l’acquisition de jeton est recommandée.

Si votre application est un client natif, le flux implicite n'est pas idéal. L’absence du cookie de session Azure AD dans le contexte d’un client natif empêche votre application de maintenir une session longue. En d’autres termes, votre application sollicite régulièrement l’utilisateur pour obtenir des jetons d’accès aux nouvelles ressources.

Si vous développez une application web qui comprend un principal et utilise une API à partir de son code principal, le flux implicite n’est pas non plus la solution idéale. D’autres modes d’octroi d’autorisation sont beaucoup plus puissants. Par exemple, l’octroi d’informations d’identification du client OAuth2 permet d’obtenir des jetons correspondant aux autorisations attribuées à l’application elle-même, et non aux délégations de l’utilisateur. Le client conserve ainsi l’accès par programmation aux ressources, même lorsqu’un utilisateur n’est pas actif dans une session et ainsi de suite. De plus, ces modes d’octroi offrent de meilleurs gages de sécurité. Par exemple, les jetons d'accès ne transitent jamais par le navigateur de l'utilisateur. Ils ne risquent donc pas d'être enregistrés dans l'historique du navigateur. L’application cliente peut également effectuer une authentification forte lorsqu’elle demande un jeton.

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

## <a name="protocol-diagram"></a>Schéma de protocole

Le diagramme suivant montre à quoi ressemble le flux implicite de connexion complet. Les sections qui suivent décrivent chaque étape plus en détail.

![Diagramme montrant le flux de connexion implicite](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Envoyer la requête de connexion

Pour connecter une première fois l’utilisateur à votre application, vous pouvez envoyer une demande d’authentification [OpenID Connect](v2-protocols-oidc.md) et obtenir un jeton `id_token` à partir du point de terminaison de la plateforme d’identités Microsoft.

> [!IMPORTANT]
> Pour demander un jeton d’ID ou d’accès avec succès, le flux d’octroi implicite correspondant à l’inscription d’application sur la page [Inscriptions d’applications du Portail Azure](https://go.microsoft.com/fwlink/?linkid=2083908) doit être activé. Pour ce faire, sélectionnez **Jetons d’ID** et/ou **Jetons d’accès** sous la section **Octroi implicite**. S’il n’est pas activé, une erreur `unsupported_response` est retournée : **La valeur fournie pour le paramètre d’entrée 'response_type' n’est pas autorisée pour ce client. La valeur attendue est ’code’**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Pour tester la connexion à l’aide du flux implicite, cliquez sur <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Une fois que vous êtes connecté, votre navigateur doit être redirigé vers `https://localhost/myapp/` avec une valeur `id_token` dans la barre d’adresse.
>

| Paramètre | Type | Description |
| --- | --- | --- |
| `tenant` | Obligatoire |La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application. Les valeurs autorisées sont `common`, `organizations`, `consumers` et les identificateurs du client. Pour plus d’informations, consultez les [principes de base du protocole](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Obligatoire | L’ID (client) d’application attribué à votre application par la page [Inscriptions d’applications du portail Azure](https://go.microsoft.com/fwlink/?linkid=2083908). |
| `response_type` | Obligatoire |Doit inclure `id_token` pour la connexion à OpenID Connect. Il peut également inclure l’élément response_type `token`. L’utilisation de l’élément `token` permet ici à votre application de recevoir immédiatement un jeton d’accès à partir du point de terminaison d’autorisation sans avoir à exécuter une deuxième requête sur le point de terminaison d’autorisation. Si vous utilisez l’élément response_type `token`, le paramètre `scope` doit contenir une étendue indiquant la ressource pour laquelle le jeton doit être émis (par exemple, user.read sur Microsoft Graph).  |
| `redirect_uri` | recommandé |L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| `scope` | Obligatoire |Une liste d’[étendues](v2-permissions-and-consent.md) séparées par des espaces. Pour OpenID Connect (id_tokens), vous devez inclure l’étendue `openid`, qui correspond à l’autorisation de connexion dans l’interface utilisateur de consentement. Vous pouvez également inclure les étendues `email` et `profile` permettant d’accéder à des données utilisateur supplémentaires. Vous pouvez aussi inclure d’autres étendues dans cette requête pour solliciter le consentement sur diverses ressources, si un jeton d’accès est demandé. |
| `response_mode` | facultatif |Spécifie la méthode à utiliser pour envoyer le jeton résultant à votre application. Par défaut demande simplement un jeton d’accès, mais fragmente si la requête inclut un jeton id_token. |
| `state` | recommandé |Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher les falsifications de requête intersite](https://tools.ietf.org/html/rfc6749#section-10.12). La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| `nonce` | Obligatoire |Une valeur incluse dans la requête, générée par l’application, qui sera intégrée dans le jeton id_token résultant en tant que revendication. L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons. La valeur est généralement une valeur unique, aléatoire pouvant être utilisé pour identifier l’origine de la requête. Nécessaire uniquement lorsqu’un jeton id_token est demandé. |
| `prompt` | facultatif |Indique le type d’interaction utilisateur requis. Les seules valeurs valides pour l’instant sont « login », « none », « select_account » et « consent ». `prompt=login` oblige l'utilisateur à saisir ses informations d'identification lors de cette requête, annulant de fait l'authentification unique. Avec `prompt=none`, c’est le comportement inverse. Cette valeur vous garantit qu’aucune invite interactive d’aucune sorte n’est présentée à l’utilisateur. Si la demande ne peut pas être exécutée en mode silencieux au moyen d’une authentification unique, le point de terminaison de la plateforme d’identités Microsoft renvoie une erreur. `prompt=select_account` envoie l’utilisateur vers un sélecteur de compte dans lequel tous les comptes mémorisés dans la session seront affichés. `prompt=consent` déclenche l’affichage de la boîte de dialogue de consentement OAuth après la connexion de l’utilisateur, afin de lui demander d’octroyer des autorisations à l’application. |
| `login_hint`  |facultatif |Peut être utilisé pour remplir au préalable le champ réservé au nom d’utilisateur/à l’adresse électronique de la page de connexion de l’utilisateur si vous connaissez déjà son nom d’utilisateur. Les applications utilisent souvent ce paramètre au cours de la réauthentification, après avoir extrait le nom d’utilisateur à partir d’une connexion précédente à l’aide de la revendication `preferred_username`.|
| `domain_hint` | facultatif |S’il est inclus, ce paramètre ignore le processus de découverte par e-mail auquel l’utilisateur doit se soumettre sur la page de connexion, ce qui améliore légèrement l’expérience utilisateur. Cela est couramment utilisé pour les applications métier qui fonctionnent dans un locataire unique, où elles fournissent un nom de domaine au sein d’un locataire donné.  L’utilisateur est transféré vers le fournisseur de fédération pour ce locataire.  Notez que cela empêchera les invités de se connecter à cette application.  |

À ce stade, l’utilisateur est invité à saisir ses informations d’identification et à exécuter l’authentification. Le point de terminaison de la plateforme d’identités Microsoft s’assure également que l’utilisateur a accepté les autorisations indiquées dans le paramètre de requête `scope`. Si l’utilisateur n’a accepté **aucune** de ces autorisations, il lui demande de corriger ce manquement. Pour plus d’informations, consultez [Autorisations, consentement et applications multi-locataires](v2-permissions-and-consent.md).

Une fois que l’utilisateur a procédé à l’authentification et accordé son consentement, le point de terminaison de la plateforme d’identités Microsoft renvoie une réponse à votre application à l’URI `redirect_uri` indiqué à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

#### <a name="successful-response"></a>Réponse correcte

Une réponse correcte utilisant `response_mode=fragment` et `response_type=id_token+token` ressemble à ceci (des sauts de ligne sont ici insérés pour une meilleure lisibilité) :

```HTTP
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Paramètre | Description |
| --- | --- |
| `access_token` |Inclus si `response_type` inclut `token`. Le jeton d'accès que l’application a demandé. Le jeton d’accès ne doit pas être décodé ou inspecté ; il doit être traité comme une chaîne opaque. |
| `token_type` |Inclus si `response_type` inclut `token`. Sera toujours `Bearer`. |
| `expires_in`|Inclus si `response_type` inclut `token`. Indique, en secondes, la durée de validité du jeton pour la mise en cache. |
| `scope` |Inclus si `response_type` inclut `token`. Indique la ou les étendues pour lesquelles le jeton access_token sera valide. Peut ne pas inclure toutes les étendues demandées, si elles n’étaient pas applicables à l’utilisateur (dans le cas des étendues spécifiques à Azure AD demandées au moment de la connexion avec un compte personnel). |
| `id_token` | Un jeton Web JSON signé (JWT). L’application peut décoder les segments de ce jeton, afin de demander des informations relatives à l’utilisateur qui s’est connecté. L’application peut mettre en cache les valeurs et les afficher, mais elle ne peut pas les utiliser pour les limites d’autorisation ou de sécurité. Pour en savoir plus sur id_tokens, consultez [`id_token reference`](id-tokens.md). <br> **Remarque :** Fourni uniquement si l’étendue `openid` a été demandée. |
| `state` |Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

#### <a name="error-response"></a>Réponse d’erreur

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri` , de manière à ce que l’application puisse les traiter de manière appropriée :

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| --- | --- |
| `error` |Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreurs se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| `error_description` |Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Obtention des jetons d’accès en mode silencieux en arrière-plan

Maintenant que vous avez connecté l’utilisateur dans votre application à page unique, vous pouvez obtenir silencieusement des jetons d’accès destinés à appeler des API web sécurisées à l’aide de la plateforme d’identités Microsoft, comme [Microsoft Graph](https://developer.microsoft.com/graph). Même si vous avez déjà reçu un jeton utilisant l’élément response_type `token`, vous pouvez utiliser cette méthode pour acquérir des jetons vers des ressources supplémentaires sans avoir à demander à l’utilisateur de se reconnecter.

Dans le flux normal OpenID Connect/OAuth, il vous faut transmettre une demande au point de terminaison `/token` de la plateforme d’identités Microsoft. Toutefois, le point de terminaison de la plateforme d’identités Microsoft ne prend pas en charge les demandes CORS. Il est donc hors de question d’effectuer des appels AJAX afin d’obtenir et d’actualiser des jetons. Au lieu de cela, vous pouvez utiliser le flux implicite d’un iFrame masqué afin d’obtenir de nouveaux jetons pour d’autres API web :

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Pour plus d’informations sur les paramètres de requête dans l’URL, consultez [Envoyer la requête de connexion](#send-the-sign-in-request).

> [!TIP]
> Essayez de copier et coller la requête ci-dessous dans un onglet de navigateur ! (N'oubliez pas de remplacer les valeurs `login_hint` par la valeur correcte pour votre utilisateur)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Grâce au paramètre `prompt=none` , cette requête va immédiatement réussir ou échouer, avant de revenir vers votre application. Une réponse correcte sera envoyée à votre application, à l’`redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

#### <a name="successful-response"></a>Réponse correcte

Une réponse correcte utilisant `response_mode=fragment` se présente ainsi :

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Paramètre | Description |
| --- | --- |
| `access_token` |Inclus si `response_type` inclut `token`. Jeton d’accès demandé par l’application, dans ce cas Microsoft Graph. Le jeton d’accès ne doit pas être décodé ou inspecté ; il doit être traité comme une chaîne opaque. |
| `token_type` | Sera toujours `Bearer`. |
| `expires_in` | Indique, en secondes, la durée de validité du jeton pour la mise en cache. |
| `scope` | Indique la ou les étendues pour lesquelles le jeton access_token sera valide. Peut ne pas inclure toutes les étendues demandées, si elles n’étaient pas applicables à l’utilisateur (dans le cas des étendues spécifiques à Azure AD demandées au moment de la connexion avec un compte personnel). |
| `id_token` | Un jeton Web JSON signé (JWT). Inclus si `response_type` inclut `id_token`. L’application peut décoder les segments de ce jeton, afin de demander des informations relatives à l’utilisateur qui s’est connecté. L’application peut mettre en cache les valeurs et les afficher, mais elle ne peut pas les utiliser pour les limites d’autorisation ou de sécurité. Pour en savoir plus sur id_tokens, consultez le document de [`id_token`référence](id-tokens.md). <br> **Remarque :** Fourni uniquement si l’étendue `openid` a été demandée. |
| `state` |Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

#### <a name="error-response"></a>Réponse d’erreur

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri` , de manière à ce que l’application puisse les traiter de manière appropriée. Dans le cas de `prompt=none`, une erreur se présentera généralement ainsi :

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paramètre | Description |
| --- | --- |
| `error` |Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreurs se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| `error_description` |Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

Si vous recevez cette erreur dans la requête iFrame, l’utilisateur doit se connecter de nouveau de manière interactive, ceci pour récupérer un nouveau jeton. Vous êtes invité à gérer ce cas de la manière la plus appropriée pour votre application.

## <a name="refreshing-tokens"></a>Actualisation des jetons

L’octroi implicite ne fournit pas de jetons d’actualisation. Les `id_token` et les `access_token` expirant après une courte période, votre application doit être préparée à les actualiser de manière régulière. Pour actualiser chaque type de jeton, vous pouvez exécuter la demande iFrame masquée ci-dessus à l’aide du paramètre `prompt=none` afin de contrôler le comportement de la plateforme d’identités. Si vous souhaitez recevoir un nouvel élément `id_token`, veillez à utiliser `id_token` dans le `response_type` et le `scope=openid`, ainsi qu’un paramètre `nonce`.

## <a name="send-a-sign-out-request"></a>Envoi d’une demande de déconnexion

L’élément `end_session_endpoint` OpenID Connect permet à votre application d’envoyer une demande au point de terminaison de la plateforme d’identités Microsoft pour mettre fin à la session d’un utilisateur et effacer les cookies définis par le point de terminaison de la plateforme d’identités Microsoft. Pour déconnecter complètement un utilisateur d’une application web, votre application doit mettre fin à sa propre session avec l’utilisateur (généralement en vidant un cache de jeton ou en supprimant les cookies), puis rediriger le navigateur vers :

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Paramètre | Type | Description |
| --- | --- | --- |
| `tenant` |Obligatoire |La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application. Les valeurs autorisées sont `common`, `organizations`, `consumers` et les identificateurs du client. Pour plus d’informations, consultez les [principes de base du protocole](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | recommandé | URL vers laquelle l’utilisateur doit être redirigé après la déconnexion. Cette valeur doit correspondre à l’un des URI de redirection inscrits pour l’application. Si elle n’est pas incluse, le point de terminaison de la plateforme d’identités Microsoft affiche un message générique. |

## <a name="next-steps"></a>Étapes suivantes

* Consultez les [exemples JS MSAL](sample-v2-code.md) pour commencer à coder.

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
