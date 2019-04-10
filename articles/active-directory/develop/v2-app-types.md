---
title: Types d’applications pour la plateforme d’identité Microsoft | Azure
description: Les types d’applications et les scénarios pris en charge par le point de terminaison Microsoft identity platform (v2.0).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2019
ms.author: celested
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562b45f30bf057feba38a716e040ab2efc15da90
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59283322"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Types d’applications pour la plateforme d’identité Microsoft

Le point de terminaison Microsoft identity platform (v2.0) prend en charge l’authentification pour de multiples architectures d’applications modernes, tous basés sur des protocoles standard [OAuth 2.0 ou OpenID Connect](active-directory-v2-protocols.md). Cet article décrit les types d’applications que vous pouvez générer à l’aide de plateforme d’identité Microsoft, quel que soit votre plateforme ou langage par défaut. Les informations sont conçues pour vous aider à comprendre les principaux scénarios avant [commencer à travailler avec le code](v2-overview.md#getting-started).

> [!NOTE]
> Le point de terminaison Microsoft identity plateforme ne prend pas en charge tous les scénarios d’Azure Active Directory (Azure AD) et les fonctionnalités. Pour déterminer si vous devez utiliser le point de terminaison Microsoft identity plateforme, consultez les [limitations de plateforme d’identité Microsoft](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Concepts de base

Vous devez inscrire chaque application qui utilise le point de terminaison Microsoft identity plateforme dans le nouveau [portail inscriptions des applications](https://go.microsoft.com/fwlink/?linkid=2083908). Le processus d’inscription des applications collecte les valeurs suivantes et les affecte à votre application :

* Un **ID d’Application (client)** qui identifie de façon unique votre application
* un **URI de redirection** que vous pouvez utiliser pour renvoyer les réponses à votre application ;
* Quelques autres types de comptes de valeurs spécifiques au scénario tel que pris en charge.

Pour en savoir plus, découvrez comment [inscrire une application](quickstart-register-app.md).

Une fois que l’application est inscrite, l’application communique avec la plateforme d’identité Microsoft en envoyant des requêtes au point de terminaison. Nous fournissons les infrastructures et les bibliothèques open source qui gèrent les détails de ces requêtes. Vous avez également la possibilité d’implémenter la logique d’authentification vous-même en créant des requêtes à ces points de terminaison :

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Applications à page unique (Javascript)

De nombreuses applications modernes disposent d’un frontend d’application à page unique écrit principalement en JavaScript. Souvent, il est écrit à l’aide d’une infrastructure telle qu’AngularJS, Ember.js ou Durandal.js. Le point de terminaison Microsoft identity plateforme prend en charge ces applications à l’aide de la [flux implicite OAuth 2.0](v2-oauth2-implicit-grant-flow.md).

Dans ce flux, l’application reçoit des jetons directement à partir de la plateforme Microsoft identity autoriser le point de terminaison, sans exécuter d’échanges de serveur à serveur. Tout traitement de logique d'authentification et de gestion de sessions est entièrement exécuté dans le client javascript, sans redirections de pages supplémentaires.

![Flux d’authentification implicite](./media/v2-app-types/convergence-scenarios-implicit.svg)

Pour voir ce scénario en action, essayez l’un des exemples de code d’application à page unique dans le [plateforme d’identité Microsoft mise en route](v2-overview.md#getting-started) section.

## <a name="web-apps"></a>les applications web

Pour les applications web (.NET, PHP, Java, Ruby, Python, Node, etc.) auxquelles l’utilisateur accède par le biais d’un navigateur, vous pouvez utiliser [OpenID Connect](active-directory-v2-protocols.md) pour la connexion de l’utilisateur. Dans OpenID Connect, l’application web reçoit un jeton d’ID. Un jeton d’ID est un jeton de sécurité qui vérifie l’identité de l’utilisateur et fournit des informations le concernant sous la forme de revendications :

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Pour en savoir plus sur différents types de jetons utilisés dans le point de terminaison Microsoft identity plateforme sont disponibles dans le [jeton d’accès](access-tokens.md) référence et [référence de jeton id_token](id-tokens.md)

Dans les applications de serveur web, le flux d’authentification de connexion respecte cette procédure de niveau supérieur :

![Flux d’authentification d’application web](./media/v2-app-types/convergence-scenarios-webapp.svg)

Vous pouvez garantir l’identité de l’utilisateur en validant le jeton d’ID avec une clé de signature publique reçue du point de terminaison Microsoft identity platform. Un cookie de session qui peut être utilisé pour identifier l’utilisateur sur les requêtes de page suivantes est défini.

Pour voir ce scénario en action, essayez un des exemples de code de connexion d’application web dans le [plateforme d’identité Microsoft mise en route](v2-overview.md#getting-started) section.

En plus de la connexion simple, une application de serveur web peut également nécessiter l’accès à un autre service Web, comme une API REST. Dans ce cas, l’application de serveur web s’engager dans un flux OpenID Connect et OAuth 2.0 à l’aide du [flux de code d’autorisation OAuth 2.0](active-directory-v2-protocols.md). Pour en savoir plus sur ce scénario, découvrez comment [la bien démarrer avec les applications web et des API web](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>API Web

Vous pouvez utiliser le point de terminaison Microsoft identity platform pour sécuriser les services web, tels que les API Web RESTful de votre application. En lieu et place des jetons d’ID et des cookies de session, une API Web utilise les jetons d’accès OAuth 2.0 pour sécuriser les données et authentifier les requêtes entrantes. L’appelant d’une API web ajoute un jeton d’accès dans l’en-tête d’autorisation d’une requête HTTP de la manière suivante :

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L’API web utilise le jeton d’accès pour vérifier l’identité de l’appelant de l’API et extraire des informations à son sujet à partir de revendications encodées dans le jeton d’accès. Pour en savoir plus sur différents types de jetons utilisés dans le point de terminaison Microsoft identity plateforme sont disponibles dans le [jeton d’accès](access-tokens.md) référence et [référence de jeton id_token](id-tokens.md)

Une API web peut octroyer aux utilisateurs la possibilité d’accepter/de refuser des fonctionnalités ou données spécifiques en exposant des autorisations (également appelées [étendues](v2-permissions-and-consent.md)). Pour qu’une application appelante puisse acquérir l’autorisation à une étendue, l’utilisateur doit accepter l’étendue au cours d’un flux. Le point de terminaison Microsoft identity plateforme demande à l’utilisateur pour l’autorisation, puis enregistre ces autorisations dans tous les jetons d’accès reçus par l’API Web. L’API web valide les jetons d’accès qu’elle reçoit à chaque appel et effectue des vérifications d’autorisation.

Une API web peut recevoir des jetons d’accès de tous types d’applications, notamment des applications de serveur web, des applications de bureau et mobiles, des applications de page unique, des démons côté serveur, et même d’autres API web. Le flux de haut niveau pour une API Web ressemble à ceci :

![Flux d’authentification d’API web](./media/v2-app-types/convergence-scenarios-webapi.svg)

Pour savoir comment sécuriser une API Web à l’aide des jetons d’accès OAuth2, consultez les exemples de code d’API Web dans le [plateforme d’identité Microsoft mise en route](v2-overview.md#getting-started) section.

Dans de nombreux cas, les API web devez également effectuer des demandes sortantes vers les autres en aval API web sécurisées par plateforme d’identité Microsoft. Pour ce faire, API web peut tirer parti de la **On-Behalf-Of** flux, ce qui permet de l’API web échanger un jeton d’accès entrant pour un autre jeton d’accès à utiliser pour les demandes sortantes. Pour plus d’informations, consultez [plateforme d’identité Microsoft et les flux OAuth 2.0 pour le compte](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Applications mobiles et natives

Les applications installées sur un appareil, comme les applications de bureau et les applications mobiles nécessitent bien souvent un accès à des services principaux ou à des API web, qui stockent les données et exécutent des fonctions pour le compte d’un utilisateur. Ces applications peuvent ajouter des fonctionnalités de connexion et d’autorisation à des services principaux à l’aide du [flux de code d’autorisation OAuth 2.0](v2-oauth2-auth-code-flow.md).

Dans ce flux, l’application reçoit un code d’autorisation du point de terminaison Microsoft identity plateforme lorsque l’utilisateur se connecte. Le code d'autorisation représente l'autorisation de l'application d'appeler les services principaux pour le compte de l'utilisateur connecté. L’application peut ensuite échanger le code d’autorisation en arrière-plan contre un jeton d’accès et un jeton d’actualisation OAuth 2.0. L’application peut utiliser le jeton d’accès pour s’authentifier sur des API web dans des requêtes HTTP et solliciter le jeton d’actualisation afin de récupérer de nouveaux jetons d’accès une fois les anciens expirés.

![Flux d’authentification d’applications native](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Applications démons et côté serveur

Les applications qui contiennent des processus de longue durée ou qui fonctionnent sans interaction d’un utilisateur doivent également disposer d’un moyen d’accès aux ressources sécurisées, comme les API web. Ces applications peuvent s'authentifier et récupérer des jetons à l'aide de l'identité d'application plutôt qu'avec l'identité déléguée d'un utilisateur avec le flux des informations d'identification du client OAuth 2.0. Vous pouvez prouver l’identité de l’application à l’aide d’une clé secrète client ou d’un certificat. Pour plus d’informations, consultez [l’authentification auprès de plateforme d’identité Microsoft dans les applications de démon avec certificats](https://azure.microsoft.com/resources/samples/active-directory-dotnet-daemon-certificate-credential/).

Dans ce flux, l’application interagit directement avec le `/token` point de terminaison pour obtenir l’accès :

![Flux d’authentification d’applications démons](./media/v2-app-types/convergence-scenarios-daemon.svg)

Pour créer une application démon, consultez la [documentation sur les informations d’identification des clients](v2-oauth2-client-creds-grant-flow.md) ou consultez un [exemple d’application .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
