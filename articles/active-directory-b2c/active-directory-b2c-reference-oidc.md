---
title: Connexion web avec OpenID Connect - Azure Active Directory B2C | Microsoft Docs
description: Créer des applications web à l’aide du protocole d’authentification OpenID Connect dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 85639e2648131f9475ad2ae77f31d43e64bf82e7
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509204"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Connexion web avec OpenID Connect dans Azure Active Directory B2C

OpenID Connect est un protocole d’authentification basé sur OAuth 2.0, qui peut être utilisé pour connecter de façon sécurisée des utilisateurs à des applications web. En utilisant l’implémentation d’OpenID Connect d’Azure Active Directory B2C (Azure AD B2C), vous pouvez sous-traiter l’inscription, la connexion et d’autres tâches de gestion des identités de vos applications web à Azure Active Directory. Ce guide explique comment procéder, indépendamment du langage. Il explique comment envoyer et recevoir des messages HTTP sans utiliser l’une de nos bibliothèques open source.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) étend le protocole *d’autorisation* OAuth 2.0 pour l’utiliser en tant que protocole *d’authentification*. Ce protocole d’authentification vous permet d’effectuer l’authentification unique. Il introduit le concept d’un *jeton d’ID*, ce qui permet au client de vérifier l’identité de l’utilisateur et obtenir des informations de profil de base sur l’utilisateur.

Parce qu’il étend OAuth 2.0, il permet également aux applications d’acquérir en toute sécurité *de jetons d’accès*. Vous pouvez utiliser des jetons d’accès pour accéder aux ressources qui sont sécurisées par un [serveur d’autorisation](active-directory-b2c-reference-protocols.md). OpenID Connect est recommandée si vous créez une application web qui a hébergé sur un serveur et accessibles via un navigateur. Si vous souhaitez ajouter la gestion des identités pour vos appareils mobiles ou applications de bureau à l’aide d’Azure AD B2C, vous devez utiliser [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) au lieu d’OpenID Connect. Pour plus d’informations sur les jetons, consultez le [vue d’ensemble de jetons dans Azure Active Directory B2C](active-directory-b2c-reference-tokens.md)

Azure AD B2C étend le protocole OpenID Connect standard pour proposer plus qu’une simple authentification et une simple autorisation. Il introduit le [paramètre de flux utilisateur](active-directory-b2c-reference-policies.md), ce qui vous permet d’utiliser OpenID Connect pour ajouter l’utilisateur des expériences à votre application, comme inscription, connexion et gestion de profil.

## <a name="send-authentication-requests"></a>Envoi de demandes d’authentification

Lorsque votre application web doit authentifier l’utilisateur et exécuter un flux d’utilisateur, il peut diriger l’utilisateur vers le `/authorize` point de terminaison. L’utilisateur prend des mesures selon le flux de l’utilisateur.

Dans cette demande, le client indique les autorisations dont il a besoin d’acquérir de l’utilisateur dans le `scope` paramètre et le flux d’utilisateur s’exécute le `p` paramètre. Trois exemples sont fournis dans les sections suivantes (avec des sauts de ligne pour une meilleure lisibilité), chacun utilisant un flux utilisateur différent. Pour avoir une idée du fonctionnement de chaque demande, essayez de coller la demande dans un navigateur et exécutez-la. Vous pouvez remplacer `fabrikamb2c` avec le nom de votre client si vous en avez pas et que vous avez créé un flux utilisateur.

#### <a name="use-a-sign-in-user-flow"></a>Utilisation d’un flux d’utilisateur de connexion
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-user-flow"></a>Utilisation d’un flux d’utilisateur d’inscription
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-user-flow"></a>Utilisation d’un flux d’utilisateur de modification de profil
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Paramètre | Obligatoire | Description |
| --------- | -------- | ----------- |
| client_id | Oui | L’ID d’application qui le [Azure portal](https://portal.azure.com/) affecté à votre application. |
| response_type | Oui | Doit inclure un jeton d’ID pour OpenID Connect. Si votre application web doit également des jetons pour appeler une API web, vous pouvez utiliser `code+id_token`. |
| redirect_uri | Non | Le `redirect_uri` paramètre de votre application, où les réponses d’authentification peuvent être envoyés et reçus par votre application. Il doit correspondre exactement à un de le `redirect_uri` paramètres que vous avez inscrite dans le portail Azure, à ceci près qu’il doit être codée URL. |
| scope | Oui | Une liste d’étendues séparées par des espaces. L’étendue `openid` indique une autorisation pour connecter l’utilisateur et obtenir des données relatives à l’utilisateur sous la forme de jetons d’ID. Le `offline_access` étendue est facultative pour les applications web. Il indique que votre application a besoin un *jeton d’actualisation* d’un accès étendu aux ressources. |
| response_mode | Non | La méthode qui est utilisée pour renvoyer le code d’autorisation résultant à votre application. Il peut s’agir de `query`, `form_post` ou `fragment`.  Le mode de réponse `form_post` est recommandé pour une sécurité optimale. |
| state | Non | Une valeur incluse dans la requête qui est également renvoyée dans la réponse de jeton. Il peut s’agir d’une chaîne du contenu de votre choix. Une valeur unique générée de manière aléatoire est généralement utilisée pour empêcher les falsifications de requête intersite. L’état est également utilisé pour encoder les informations sur l’état de l’utilisateur dans l’application avant la demande d’authentification, comme la page sur qu'ils se trouvaient. |
| nonce | Oui | Une valeur incluse dans la demande (générée par l’application) qui est incluse dans le jeton d’ID résultant en tant que revendication. L’application peut ensuite vérifier cette valeur pour atténuer les attaques par relecture de jeton. La valeur est généralement une valeur unique aléatoire qui peut être utilisée pour identifier l’origine de la demande. |
| p | Oui | Le flux de l’utilisateur qui est exécuté. Il s’agit du nom d’un flux d’utilisateur qui est créé dans votre locataire Azure AD B2C. Le nom du flux utilisateur doit commencer par `b2c\_1\_`. |
| prompt | Non | Type d’interaction utilisateur demandée. La seule valeur valide pour l’instant est `login`, qui oblige l’utilisateur à saisir ses informations d’identification sur cette demande. |

À ce stade, l’utilisateur est invité à terminer le flux de travail. L’utilisateur peut avoir à entrer son nom d’utilisateur et le mot de passe, inscrivez-vous avec une identité sociale ou la connexion à l’annuaire. Il peut y avoir autant d’étapes en fonction de la façon dont le flux de l’utilisateur est défini.

Une fois que l’utilisateur termine le flux de l’utilisateur, une réponse est retournée à votre application à l’indiquée `redirect_uri` paramètre, à l’aide de la méthode qui est spécifiée dans le `response_mode` paramètre. La réponse est la même pour chacun des cas précédents, indépendamment du flux utilisateur.

Une réponse réussie utilisant `response_mode=fragment` se présenterait ainsi :

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| --------- | ----------- |
| id_token | Le jeton d’ID demandé par l’application. Vous pouvez utiliser le jeton d'ID pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. |
| code | Le code d’autorisation demandé par l’application, si vous avez utilisé `response_type=code+id_token`. L’application peut utiliser le code d’autorisation pour demander un jeton d’accès pour une ressource cible. Codes d’autorisation est généralement expirent après 10 minutes environ. |
| state | Si un paramètre `state` est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que le `state` valeurs de la demande et la réponse sont identiques. |

Réponses d’erreur peuvent également être envoyés à la `redirect_uri` paramètre afin que l’application peut traiter de manière appropriée :

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Paramètre | Description |
| --------- | ----------- |
| error | Un code qui peut être utilisé pour classer les types d’erreurs qui se produisent. |
| error_description | Un message d’erreur spécifique qui peut aider à identifier la cause racine d’une erreur d’authentification. |
| state | Si un paramètre `state` est inclus dans la demande, la même valeur doit apparaître dans la réponse. L’application doit vérifier que le `state` valeurs de la demande et la réponse sont identiques. |

## <a name="validate-the-id-token"></a>Validation du jeton d’ID

La réception d’un jeton d’ID à elle seule n’est pas suffisante pour authentifier l’utilisateur. Valider la signature du jeton d’ID et vérifier les revendications du jeton selon les exigences de votre application. Azure AD B2C utilise les [jetons Web JSON (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) et le chiffrement de clés publiques pour signer les jetons et vérifier leur validité. Il existe de nombreuses bibliothèques open source pour valider les jetons JWT en fonction de votre langage préféré. Nous vous recommandons d’explorer ces options plutôt que d’implémenter votre propre logique de validation. 

Azure AD B2C a un OpenID Connect métadonnées point de terminaison, ce qui permet à une application obtenir des informations sur Azure AD B2C lors de l’exécution. Ces informations incluent les points de terminaison, le contenu des jetons et les clés de signature de jetons. Il existe un document de métadonnées JSON pour chaque flux utilisateur dans votre locataire B2C. Par exemple, le document de métadonnées pour le flux utilisateur `b2c_1_sign_in` dans `fabrikamb2c.onmicrosoft.com` se trouve à l’emplacement suivant :

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Une des propriétés de ce document de configuration est `jwks_uri`, dont la valeur pour le même flux utilisateur serait :

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Pour déterminer quels flux utilisateur utilisé pour la connexion d’un ID de jeton (et à partir de laquelle obtenir les métadonnées), vous avez deux options. Tout d’abord, le nom du flux utilisateur est inclus dans la revendication `acr` du jeton d’ID. L’autre option consiste à coder le flux utilisateur dans la valeur du paramètre `state` lors de l’émission de la requête, puis à la décoder pour déterminer le flux utilisateur qui a été utilisé. Les 2 méthodes sont valides.

Une fois que vous avez acquis le document de métadonnées à partir du point de terminaison de métadonnées OpenID Connect, vous pouvez utiliser les clés publiques RSA 256 pour valider la signature du jeton d’ID. Il peut y avoir plusieurs clés répertoriées à ce point de terminaison, chacune étant identifiée par un `kid` de revendication. L’en-tête de ce jeton d’ID contient également une revendication `kid`, qui indique quelle clé a été utilisée pour signer le jeton d’ID.

Après avoir validé la signature du jeton d’ID, vous devez vérifier plusieurs revendications. Exemple :

- Validez la revendication `nonce` afin d’empêcher les attaques par relecture de jetons. Sa valeur doit correspondre à ce que vous avez spécifié dans la requête de connexion.
- Valider la `aud` revendication afin de vérifier que le jeton d’ID a été émis pour votre application. Sa valeur doit être l’ID d’application de votre application.
- Valider la `iat` et `exp` de revendications pour vous assurer que le jeton d’ID n’a pas expiré.

Vous devez également effectuer plusieurs autres validations. Les validations sont décrites en détail dans le [spécifications principales d’OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). En fonction de votre scénario, vous pouvez également valider des revendications supplémentaires. Voici quelques validations courantes :

- Vérifier que l’utilisateur / l’organisation s’est inscrit pour l’application.
- Vérifier que l’utilisateur dispose de l’autorisation/des privilèges appropriés.
- S’assurer de l’utilisation d’une force certaine d’authentification, comme Azure Multi-Factor Authentication.

Après avoir validé le jeton d’ID, vous pouvez commencer une session avec l’utilisateur. Vous pouvez utiliser les revendications dans le jeton d’ID pour obtenir des informations sur l’utilisateur dans votre application. Les utilisations de ces informations sont notamment l’affichage, les enregistrements et les autorisations.

## <a name="get-a-token"></a>Obtention d’un jeton

Si vous avez besoin de votre application web pour exécuter uniquement les flux d’utilisateurs, vous pouvez ignorer les sections suivantes. Ces sections sont applique uniquement aux web applications qui doivent effectuer des appels authentifiés à une API web et sont également protégées par Azure AD B2C.

Vous pouvez échanger le code d’autorisation que vous avez acquis (en utilisant `response_type=code+id_token`) contre un jeton sur la ressource souhaitée en envoyant une demande `POST` au point de terminaison `/token`. Actuellement, la seule ressource pour laquelle vous pouvez demander un jeton est votre web back-end de l’application API. La convention pour demander un jeton à vous-même consiste à utiliser des ID de client de votre application en tant que l’étendue :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Paramètre | Obligatoire | Description |
| --------- | -------- | ----------- |
| p | Oui | Le flux utilisateur qui a été utilisé pour obtenir le code d’autorisation. Vous ne pouvez pas utiliser un flux d’utilisateur différent dans cette demande. Ajoutez ce paramètre à la chaîne de requête, et non vers le corps POST. |
| client_id | Oui | L’ID d’application qui le [Azure portal](https://portal.azure.com/) affecté à votre application. |
| grant_type | Oui | Le type d’octroi, qui doit être `authorization_code` pour le flux de code d’autorisation. |
| scope | Non | Une liste d’étendues séparées par des espaces. L’étendue `openid` indique une autorisation pour connecter l’utilisateur et obtenir des données relatives à l’utilisateur sous la forme de paramètres id_token. Il peut être utilisé pour obtenir des jetons pour votre web back-end de l’application API, qui est représentée par le même ID d’application en tant que le client. Le `offline_access` étendue indique que votre application a besoin d’un jeton d’actualisation d’un accès étendu aux ressources. |
| code | Oui | Le code d’autorisation que vous avez acquis au début du flux utilisateur. |
| redirect_uri | Oui | Le paramètre `redirect_uri` de l’application où vous avez reçu le code d’autorisation. |
| client_secret | Oui | Le secret d’application qui a été généré dans le [Azure portal](https://portal.azure.com/). Ce secret d’application est un artefact de sécurité important. Vous devez le stocker sur votre serveur de manière sécurisée. Modifiez cette clé secrète client de manière périodique. |

Un jeton de réponse de réussite se présente ainsi :

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paramètre | Description |
| --------- | ----------- |
| not_before | Heure à laquelle le jeton est considéré comme valide, en heure epoch. |
| token_type | Valeur du type de jeton. `Bearer` est le seul type pris en charge. |
| access_token | Le jeton JWT signé que vous avez demandé. |
| scope | Étendues pour lesquelles le jeton est valide. |
| expires_in | Durée de validité du jeton d’accès (en secondes). |
| refresh_token | Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons supplémentaires après l’expiration du jeton en cours. Actualiser les jetons peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées. L’étendue `offline_access` doit avoir été utilisé dans les demandes de jeton et d’autorisation afin de recevoir un jeton d’actualisation. |

Les réponses d’erreur se présentent comme ceci :

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paramètre | Description |
| --------- | ----------- |
| error | Un code qui peut être utilisé pour classer les types d’erreurs qui se produisent. |
| error_description | Un message qui peut aider à identifier la cause racine d’une erreur d’authentification. |

## <a name="use-the-token"></a>Utilisation du jeton

Un jeton d’accès étant acquis, vous pouvez maintenant l’utiliser dans les demandes effectuées à vos API web principales en l’incluant dans l’en-tête `Authorization` :

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Actualisation du jeton

Les jetons d’ID expirent après une courte période de temps. Les jetons d’actualisation après leur expiration pour continuer à accéder aux ressources. Vous pouvez actualiser un jeton, envoyez une nouvelle `POST` demande à le `/token` point de terminaison. Cette fois-ci, spécifiez le paramètre `refresh_token` au lieu du paramètre `code` :

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Paramètre | Obligatoire | Description |
| --------- | -------- | ----------- |
| p | Oui | Flux utilisateur utilisé pour obtenir le jeton d’actualisation d’origine. Vous ne pouvez pas utiliser un flux d’utilisateur différent dans cette demande. Ajoutez ce paramètre à la chaîne de requête, et non vers le corps POST. |
| client_id | Oui | L’ID d’application qui le [Azure portal](https://portal.azure.com/) affecté à votre application. |
| grant_type | Oui | Le type d’octroi, qui doit être un jeton d’actualisation pour cette partie du flux de code d’autorisation. |
| scope | Non | Une liste d’étendues séparées par des espaces. L’étendue `openid` indique une autorisation pour connecter l’utilisateur et obtenir des données relatives à l’utilisateur sous la forme de jetons d’ID. Il peut être utilisé pour envoyer des jetons à votre web back-end de l’application API, qui est représentée par le même ID d’application en tant que le client. Le `offline_access` étendue indique que votre application a besoin d’un jeton d’actualisation d’un accès étendu aux ressources. |
| redirect_uri | Non | Le paramètre `redirect_uri` de l’application où vous avez reçu le code d’autorisation. |
| refresh_token | Oui | Le jeton d’actualisation d’origine qui a été acquis dans la deuxième partie du flux. Le `offline_access` étendue doit être utilisée dans les demandes de jeton et d’autorisation afin de recevoir un jeton d’actualisation. |
| client_secret | Oui | Le secret d’application qui a été généré dans le [Azure portal](https://portal.azure.com/). Ce secret d’application est un artefact de sécurité important. Vous devez le stocker sur votre serveur de manière sécurisée. Modifiez cette clé secrète client de manière périodique. |

Un jeton de réponse de réussite se présente ainsi :

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Paramètre | Description |
| --------- | ----------- |
| not_before | Heure à laquelle le jeton est considéré comme valide, en heure epoch. |
| token_type | Valeur du type de jeton. `Bearer` est le seul type pris en charge. |
| access_token | Le jeton JWT signé qui a été demandé. |
| scope | L’étendue pour laquelle le jeton est valide. |
| expires_in | Durée de validité du jeton d’accès (en secondes). |
| refresh_token | Un jeton d’actualisation OAuth 2.0. L’application peut utiliser ce jeton pour acquérir des jetons supplémentaires après l’expiration du jeton en cours. Actualiser les jetons peuvent être utilisés pour conserver l’accès aux ressources pendant des périodes prolongées. |

Les réponses d’erreur se présentent comme ceci :

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Paramètre | Description |
| --------- | ----------- |
| error | Un code qui peut être utilisé pour classer les types d’erreurs qui se produisent. |
| error_description | Un message qui peut aider à identifier la cause racine d’une erreur d’authentification. |

## <a name="send-a-sign-out-request"></a>Envoi d’une demande de déconnexion

Lorsque vous souhaitez déconnecter l’utilisateur de l’application, il n’est pas suffisant pour effacer les cookies de l’application ou d’arrêter la session de l’utilisateur. Rediriger l’utilisateur vers Azure AD B2C pour vous déconnecter. Si vous ne parvenez pas à le faire, l’utilisateur peut être en mesure de s’authentifier de nouveau à votre application sans saisir de nouveau leurs informations d’identification.

Vous pouvez simplement rediriger l’utilisateur vers le `end_session` point de terminaison qui est répertorié dans le document de métadonnées OpenID Connect décrit précédemment :

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Paramètre | Obligatoire | Description |
| --------- | -------- | ----------- |
| p | Oui | Flux utilisateur que vous voulez utiliser pour déconnecter l’utilisateur de votre application. |
| post_logout_redirect_uri | Non | L’URL vers laquelle l’utilisateur doit être redirigé après déconnexion réussie. Si elle n’est pas incluse, Azure AD B2C affiche un message générique à l’utilisateur. |

Redirection de l’utilisateur pour le `end_session` point de terminaison efface certains d’unique état d’authentification l’utilisateur avec Azure AD B2C, mais elle ne déconnecte l’utilisateur en dehors de leur session (IDP) de fournisseur d’identité sociale. Si l’utilisateur sélectionne le même fournisseur d’identité au cours d’une suivantes signe, ils sont réauthentifiés sans entrer leurs informations d’identification. Si un utilisateur veut se déconnecter de l’application, n’implique pas forcément qu’il souhaite se déconnecter de leur compte Facebook. Toutefois, si les comptes locaux sont utilisés, la session utilisateur se termine correctement.

