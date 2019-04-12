---
title: Sécuriser des applications à page unique utilisant le flux implicite de la plateforme Microsoft identity | Azure
description: Création d’applications web à l’aide de la mise en œuvre de la plateforme Microsoft identity du flux implicite pour les applications à page unique.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 453a3316288cbc0b07d82e2fad9ecc7c3d353e9b
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501312"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Flux d’octroi implicite et la plateforme d’identité Microsoft

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Avec le point de terminaison Microsoft identity plateforme, vous pouvez connecter des utilisateurs dans vos applications à page unique avec à la fois personnelles et comptes professionnels ou scolaires à partir de Microsoft. Les applications à page unique et autres applications JavaScript qui s’exécutent principalement dans un navigateur présentent des problématiques spécifiques liées à l’authentification :

* Les caractéristiques de sécurité de ces applications sont considérablement différentes de celles des applications web traditionnelles basées sur serveur.
* De nombreux serveurs d’autorisation et fournisseurs d’identité ne prennent pas en charge les demandes CORS.
* Chacune des redirections à partir de l’application du navigateur plein écran perturbe de manière assez importante l’expérience utilisateur.

Pour ces applications (AngularJS, Ember.js, React.js, etc.), plateforme d’identité Microsoft prend en charge le flux d’octroi implicite OAuth 2.0. Le flux implicite est décrit dans la [spécification OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). Son principal avantage est qu’il permet à l’application obtenir des jetons à partir de la plateforme d’identité Microsoft sans effectuer d’un serveur principal exchange des informations d’identification. L’application peut alors connecter l’utilisateur, maintenir la session et obtenir des jetons pour d’autres API web, le tout dans le code JavaScript client. Quelques points de sécurité importants sont à prendre en compte lorsque vous utilisez le flux implicite, particulièrement en ce qui concerne l’emprunt d’identité du [client](https://tools.ietf.org/html/rfc6749#section-10.3) et de [l’utilisateur](https://tools.ietf.org/html/rfc6749#section-10.3).

Si vous souhaitez utiliser le flux implicite et la plateforme d’identité Microsoft pour ajouter l’authentification à votre application JavaScript, nous vous recommandons d’utiliser la bibliothèque JavaScript open source, [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js).

Toutefois, si vous préférez ne pas utiliser de bibliothèque dans votre application à page unique et envoyer vous-même des messages de protocole, suivez la procédure générale ci-dessous.

> [!NOTE]
> Pas tous les scénarios Azure Active Directory (Azure AD) et les fonctionnalités sont prises en charge par le point de terminaison Microsoft identity platform. Pour déterminer si vous devez utiliser le point de terminaison Microsoft identity plateforme, consultez les [limitations de plateforme d’identité Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Schéma de protocole

Le diagramme suivant montre à quoi ressemble le flux implicite de connexion complet. Les sections qui suivent décrivent chaque étape plus en détail.

![Couloirs OpenId Connect](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Envoyer la requête de connexion

Pour connecter initialement l’utilisateur dans votre application, vous pouvez envoyer un [OpenID Connect](v2-protocols-oidc.md) demande d’autorisation et obtenir un `id_token` du point de terminaison Microsoft identity platform.

> [!IMPORTANT]
> À demander un jeton d’ID, l’inscription d’application dans le [portail Azure - inscriptions](https://go.microsoft.com/fwlink/?linkid=2083908) page doit avoir le flux d’octroi implicite activé correctement, en sélectionnant **de jetons d’accès** et **Jetons d’ID** sous le **octroi implicite** section. S’il n’est pas activé, un `unsupported_response` erreur est retournée : **La valeur fournie pour le paramètre d’entrée 'response_type' n’est pas autorisée pour ce client. La valeur attendue est ’code’**

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

| Paramètre |  | Description |
| --- | --- | --- |
| `tenant` | required |La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application. Les valeurs autorisées sont `common`, `organizations`, `consumers` et les identificateurs du client. Pour plus d’informations, consultez les [principes de base du protocole](active-directory-v2-protocols.md#endpoints). |
| `client_id` | required | ID d’Application (client) que le [portail Azure - inscriptions](https://go.microsoft.com/fwlink/?linkid=2083908) page affecté à votre application. |
| `response_type` | required |Doit inclure `id_token` pour la connexion à OpenID Connect. Il peut également inclure l’élément response_type `token`. L’utilisation de l’élément `token` permet ici à votre application de recevoir immédiatement un jeton d’accès à partir du point de terminaison d’autorisation sans avoir à exécuter une deuxième requête sur le point de terminaison d’autorisation. Si vous utilisez l’élément response_type `token`, le paramètre `scope` doit contenir une étendue indiquant la ressource pour laquelle le jeton doit être émis. |
| `redirect_uri` | recommandé |L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. |
| `scope` | required |Une liste d’[étendues](v2-permissions-and-consent.md) séparées par des espaces. Pour OpenID Connect, vous devez inclure l’étendue `openid`, qui correspond à l’autorisation de connexion dans l’interface utilisateur de consentement. Vous pouvez également inclure les étendues `email` ou `profile` permettant d’accéder à des données utilisateur supplémentaires. Vous pouvez aussi inclure d’autres étendues dans cette requête pour solliciter le consentement sur diverses ressources. |
| `response_mode` | facultatif |Spécifie la méthode à utiliser pour envoyer le jeton résultant à votre application. Par défaut demande un jeton d’accès, mais fragmente si la requête inclut un jeton id_token. |
| `state` | recommandé |Une valeur incluse dans la requête, qui sera également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour [empêcher les falsifications de requête intersite](https://tools.ietf.org/html/rfc6749#section-10.12). La valeur d’état est également utilisée pour coder les informations sur l’état de l’utilisateur dans l’application avant la requête d’authentification, comme la page ou l’écran sur lequel ou laquelle il était positionné. |
| `nonce` | required |Une valeur incluse dans la requête, générée par l’application, qui sera intégrée dans le jeton id_token résultant en tant que revendication. L’application peut ensuite vérifier cette valeur afin de contrer les attaques par relecture de jetons. La valeur est généralement une valeur unique, aléatoire pouvant être utilisé pour identifier l’origine de la requête. Nécessaire uniquement lorsqu’un jeton id_token est demandé. |
| `prompt` | facultatif |Indique le type d’interaction utilisateur requis. Les seules valeurs valides pour l’instant sont « login », « none », « select_account » et « consent ». `prompt=login` force l’utilisateur à entrer leurs informations d’identification lors de cette requête, annulant de fait l’authentification unique. `prompt=none` est le contraire - Cela permet de garantir que l’utilisateur n’est pas présenté avec aucune invite interactive que ce soit. Si la demande ne peut pas être exécutée en mode silencieux via l’authentification unique, le point de terminaison Microsoft identity plateforme retournera une erreur. `prompt=select_account` envoie l’utilisateur à un sélecteur de compte dans lequel tous les comptes mémorisés dans la session seront affiche. `prompt=consent` déclenche la boîte de dialogue de consentement OAuth une fois que l’utilisateur se connecte, l’invitant à accorder des autorisations à l’application. |
| `login_hint`  |facultatif |Peut être utilisé pour remplir au préalable le champ réservé au nom d’utilisateur/à l’adresse électronique de la page de connexion de l’utilisateur si vous connaissez déjà son nom d’utilisateur. Les applications utilisent souvent ce paramètre au cours de la réauthentification, après avoir extrait le nom d’utilisateur à partir d’une connexion précédente à l’aide de la revendication `preferred_username` .|
| `domain_hint` | facultatif |Peut être `consumers` ou `organizations`. Si inclus, il ignore le processus de découverte par courrier électronique que l’utilisateur doit se soumettre sur la connexion dans la page, ce qui conduit à une peu plus l’expérience utilisateur. Les applications utilisent souvent ce paramètre au cours de la réauthentification, en extrayant la revendication `tid` du jeton id_token. Si la valeur de revendication `tid` est `9188040d-6c67-4c5b-b112-36a304b66dad` (le client consommateur de compte Microsoft), vous devez utiliser `domain_hint=consumers`. Sinon, vous pouvez utiliser `domain_hint=organizations` au cours de la réauthentification. |

À ce stade, l’utilisateur est invité à saisir ses informations d’identification et à exécuter l’authentification. Le point de terminaison Microsoft identity plateforme garantit également que l’utilisateur a accepté les autorisations indiquées dans le `scope` paramètre de requête. Si l’utilisateur n’a accepté **aucune** de ces autorisations, il lui demande de corriger ce manquement. Pour plus d’informations, consultez [Autorisations, consentement et applications multi-locataires](v2-permissions-and-consent.md).

Une fois que l’utilisateur s’authentifie et donne son consentement, le point de terminaison Microsoft identity plateforme renvoie une réponse à votre application à l’indiquée `redirect_uri`, à l’aide de la méthode spécifiée dans le `response_mode` paramètre.

#### <a name="successful-response"></a>Réponse correcte

Une réponse correcte utilisant `response_mode=fragment` et `response_type=id_token+token` ressemble à ceci (des sauts de ligne sont ici insérés pour une meilleure lisibilité) :

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Paramètre | Description |
| --- | --- |
| `access_token` |Inclus si `response_type` inclut `token`. Jeton d’accès demandé par l’application, dans ce cas Microsoft Graph. Le jeton d’accès ne doit pas être décodé ou inspecté, elle doit être traitée comme une chaîne opaque. |
| `token_type` |Inclus si `response_type` inclut `token`. Sera toujours `Bearer`. |
| `expires_in`|Inclus si `response_type` inclut `token`. Indique, en secondes, la durée de validité du jeton pour la mise en cache. |
| `scope` |Inclus si `response_type` inclut `token`. Indique la ou les étendues pour lesquelles le jeton access_token sera valide. Peut ne pas inclure toutes les étendues demandés, s’ils n’étaient pas applicables à l’utilisateur (dans le cas d’Azure AD uniquement les étendues demandé lorsqu’un compte personnel est utilisé pour se connecter). |
| `id_token` | Un jeton Web JSON signé (JWT). L’application peut décoder les segments de ce jeton, afin de demander des informations relatives à l’utilisateur qui s’est connecté. L’application peut mettre en cache les valeurs et les afficher, mais il ne doit pas s’appuient sur ces derniers pour toute autorisation ou les limites de sécurité. Pour en savoir plus sur id_tokens, consultez [`id_token reference`](id-tokens.md). <br> **Remarque :** Fourni uniquement si l’étendue `openid` a été demandée. |
| `state` |Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

#### <a name="error-response"></a>Réponse d’erreur

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri` , de manière à ce que l’application puisse les traiter de manière appropriée :

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paramètre | Description |
| --- | --- |
| `error` |Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| `error_description` |Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

## <a name="validate-the-idtoken"></a>Valider le jeton id_token

Réception simplement du jeton id_token n’est pas suffisante pour authentifier l’utilisateur ; Vous devez également valider la signature du jeton de l’id_token et vérifier les revendications dans le jeton selon les besoins de votre application. Le point de terminaison Microsoft identity plateforme [jetons Web JSON (Jwt)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et chiffrement à clé publique pour signer les jetons et vérifier qu’ils sont valides.

Vous pouvez décider de valider l’élément `id_token` dans le code du client, mais une pratique courante consiste à envoyer l’élément `id_token` vers un serveur principal, afin d’y appliquer la validation. Une fois que vous avez validé la signature du jeton id_token, il existe certaines revendications, que vous devrez vérifier. Pour plus d’informations, consultez la page de [`id_token`référence](id-tokens.md), notamment les sections [Validation des jetons](id-tokens.md#validating-an-id_token) et [Informations importantes sur la substitution des clés de signature](active-directory-signing-key-rollover.md). Nous vous recommandons d’utiliser une bibliothèque pour analyser et valider les jetons. Il en existe au moins une pour la plupart des langages et plateformes.

En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires. Voici quelques validations courantes :

* S’assurer que l’utilisateur/l’organisation s’est inscrit pour l’application.
* S’assurer que l’utilisateur dispose de l’autorisation/des privilèges appropriés.
* S’assurer de l’utilisation d’une force certaine d’authentification, comme une authentification multifacteur.

Une fois que vous avez validé le jeton id_token, vous pouvez démarrer une session avec l’utilisateur et utiliser les revendications du jeton id_token pour obtenir des informations sur l’utilisateur dans votre application. Ces informations peuvent être utilisées pour l’affichage, les enregistrements, personnalisation et bien plus encore.

## <a name="get-access-tokens"></a>Obtenir des jetons d’accès

Maintenant que vous avez connecté l’utilisateur dans votre application à page unique, vous pouvez obtenir des jetons d’accès pour un appel API web sécurisées par plateforme d’identité Microsoft, tels que le [Microsoft Graph](https://developer.microsoft.com/graph). Même si vous avez déjà reçu un jeton utilisant l’élément response_type `token`, vous pouvez utiliser cette méthode pour acquérir des jetons vers des ressources supplémentaires sans avoir à demander à l’utilisateur de se reconnecter.

Dans le flux normal OpenID Connect/OAuth, vous faites cela en effectuant une demande à la plateforme Microsoft identity `/token` point de terminaison. Toutefois, le point de terminaison Microsoft identity plateforme ne prend pas en charge les demandes CORS, afin d’effectuer des appels AJAX pour obtenir et jetons d’actualisation est hors de question. Au lieu de cela, vous pouvez utiliser le flux implicite d’un iFrame masqué afin d’obtenir de nouveaux jetons pour d’autres API web : 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

Pour plus d’informations sur les paramètres de requête dans l’URL, consultez [Envoyer la requête de connexion](#send-the-sign-in-request).

> [!TIP]
> Essayez de copier et coller la requête ci-dessous dans un onglet de navigateur ! (N'oubliez pas de remplacer les valeurs `login_hint` par la valeur correcte pour votre utilisateur)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

Grâce au paramètre `prompt=none` , cette requête va immédiatement réussir ou échouer, avant de revenir vers votre application. Une réponse correcte sera envoyée à votre application, à l’`redirect_uri` indiqué, à l’aide de la méthode spécifiée dans le paramètre `response_mode`.

#### <a name="successful-response"></a>Réponse correcte

Une réponse correcte utilisant `response_mode=fragment` se présente ainsi :

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Paramètre | Description |
| --- | --- |
| `access_token` |Inclus si `response_type` inclut `token`. Jeton d’accès demandé par l’application, dans ce cas Microsoft Graph. Le jeton d’accès ne doit pas être décodé ou inspecté, elle doit être traitée comme une chaîne opaque. |
| `token_type` | Sera toujours `Bearer`. |
| `expires_in` | Indique, en secondes, la durée de validité du jeton pour la mise en cache. |
| `scope` | Indique la ou les étendues pour lesquelles le jeton access_token sera valide. Peut ne pas inclure toutes les étendues demandés, s’ils n’étaient pas applicables à l’utilisateur (dans le cas d’Azure AD uniquement les étendues demandé lorsqu’un compte personnel est utilisé pour se connecter). |
| `id_token` | Un jeton Web JSON signé (JWT). Inclus si `response_type` inclut `id_token`. L’application peut décoder les segments de ce jeton, afin de demander des informations relatives à l’utilisateur qui s’est connecté. L’application peut mettre en cache les valeurs et les afficher, mais il ne doit pas s’appuient sur ces derniers pour toute autorisation ou les limites de sécurité. Pour en savoir plus sur id_tokens, consultez le document de [`id_token`référence](id-tokens.md). <br> **Remarque :** Fourni uniquement si l’étendue `openid` a été demandée. |
| `state` |Si un paramètre d’état est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que les valeurs d’état de la demande et de la réponse sont identiques. |

#### <a name="error-response"></a>Réponse d’erreur

Les réponses d’erreur peuvent également être envoyées à l’élément `redirect_uri` , de manière à ce que l’application puisse les traiter de manière appropriée. Dans le cas de `prompt=none`, une erreur se présentera généralement ainsi :

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paramètre | Description |
| --- | --- |
| `error` |Une chaîne de code d’erreur pouvant être utilisée pour classer les types d’erreur se produisant, et pouvant être utilisée pour intervenir face aux erreurs. |
| `error_description` |Un message d’erreur spécifique qui peut aider un développeur à identifier la cause principale d’une erreur d’authentification. |

Si vous recevez cette erreur dans la requête iFrame, l’utilisateur doit se connecter de nouveau de manière interactive, ceci pour récupérer un nouveau jeton. Vous êtes invité à gérer ce cas de la manière la plus appropriée pour votre application.

## <a name="validating-access-tokens"></a>Validation des jetons d’accès

Quand vous recevez un access_token, veillez à valider la signature du jeton, ainsi que les réclamations suivantes. En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires.

* Revendication **audience** : il s’agit de vérifier que le jeton était bien destiné à votre application
* **émetteur** de revendication, pour vérifier que le jeton a été émis à votre application par le point de terminaison Microsoft identity platform
* Revendications **pas avant** et **heure d’expiration** : il s’agit de vérifier que le jeton n’est pas arrivé à expiration

Pour plus d’informations sur les revendications se trouvant dans le jeton d’accès, consultez l’article [Informations de référence sur les jetons d’accès](access-tokens.md)

## <a name="refreshing-tokens"></a>Actualisation des jetons

L’octroi implicite ne fournit pas de jetons d’actualisation. Les `id_token` et les `access_token` expirant après une courte période, votre application doit être préparée à les actualiser de manière régulière. Pour actualiser chaque type de jeton, vous pouvez effectuer à l’aide de la même requête iframe le `prompt=none` paramètre pour contrôler le comportement de la plateforme identité. Si vous souhaitez recevoir un nouvel élément `id_token`, veillez à utiliser `response_type=id_token` et `scope=openid`, ainsi qu’un paramètre `nonce`.

## <a name="send-a-sign-out-request"></a>Envoi d’une demande de déconnexion

OpenID Connect `end_session_endpoint` permet à votre application envoyer une demande au point de terminaison de l’identité de plate-forme Microsoft pour terminer une session utilisateur et d’effacer les cookies définis par le point de terminaison Microsoft identity platform. Pour déconnecter complètement un utilisateur d’une application web, votre application doit mettre fin à sa propre session avec l’utilisateur (généralement en vidant un cache de jeton ou en supprimant les cookies), puis rediriger le navigateur vers :

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Paramètre |  | Description |
| --- | --- | --- |
| `tenant` |required |La valeur `{tenant}` dans le chemin d’accès de la requête peut être utilisée pour contrôler les utilisateurs qui peuvent se connecter à l’application. Les valeurs autorisées sont `common`, `organizations`, `consumers` et les identificateurs du client. Pour plus d’informations, consultez les [principes de base du protocole](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | recommandé | URL vers laquelle l’utilisateur doit être redirigé après la déconnexion. Cette valeur doit correspondre à l’un des URI de redirection inscrits pour l’application. Si ne pas inclus, l’utilisateur s’affichera un message générique par le point de terminaison Microsoft identity platform. |

## <a name="next-steps"></a>Étapes suivantes

* Consultez les [exemples JS MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) pour commencer à coder.
