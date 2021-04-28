---
title: Déplacer une application démon qui appelle des API web en production | Azure
titleSuffix: Microsoft identity platform
description: Découvrir comment passer en production une application démon qui appelle des API web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e53ae26e4ed5bf42a42daa527c81949c876ab5e1
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108064014"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Application démon appelant des API web : passage en production

Maintenant que vous savez comment obtenir et utiliser un jeton pour un appel de service à service, découvrez comment faire passer votre application en production.

## <a name="deployment---multitenant-daemon-apps"></a>Déploiement : applications démon multilocataires

Si vous êtes un éditeur de logiciels indépendant (ISV) créant une application démon qui peut s’exécuter dans plusieurs locataires, assurez-vous que l’administrateur de clients :

- approvisionne un principal de service pour l’application ;
- accorde le consentement à l’application.

Vous devrez expliquer à vos clients comment effectuer ces opérations. Pour plus d’informations, consultez [Demande de consentement d’un client entier](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>Exemples de code

# <a name="net"></a>[.NET](#tab/dotnet)

- Documentation de référence pour :
  - Instanciation de [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Appeler [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder?view=azure-dotnet&preserve-view=true).
- Autres exemples/tutoriels :
  - [microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) propose une petite application console démon .NET Core qui affiche les utilisateurs d’un locataire interrogeant Microsoft Graph.

    ![Exemple de la topologie d’une application démon](media/scenario-daemon-app/daemon-app-sample.svg)

    Le même exemple illustre également une variation avec des certificats :

    ![Exemple de la topologie d’une application démon : certificats](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) propose une application web ASP.NET MVC qui synchronise les données Microsoft Graph en utilisant l’identité de l’application plutôt que le compte d’un utilisateur. Cet exemple illustre également le processus de consentement administrateur.

    ![Topologie](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="java"></a>[Java](#tab/java)

Essayez le démarrage rapide [Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application console Java à l’aide de l’identité de l’application](quickstart-v2-java-daemon.md).

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

- Pour plus d'informations, consultez les pages suivantes :
  - Comprendre la [Configuration](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md)
  - Instanciation de [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)
  - [FORUM AUX QUESTIONS](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/faq.md)
- Autres exemples/tutoriels :
  - [Exemple démon de console de nœud MSAL](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console)

# <a name="python"></a>[Python](#tab/python)

Essayez le démarrage rapide [Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application console Python à l’aide de l’identité de l’application](quickstart-v2-python-daemon.md).

---

## <a name="next-steps"></a>Étapes suivantes

Voici quelques liens pour vous aider à en savoir plus :

# <a name="net"></a>[.NET](#tab/dotnet)

Essayez le démarrage rapide [Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application console .NET Core à l’aide de l’identité de l’application](quickstart-v2-netcore-daemon.md).

# <a name="java"></a>[Java](#tab/java)

Essayez le démarrage rapide [Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application console Java à l’aide de l’identité de l’application](quickstart-v2-java-daemon.md).

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Essayez le démarrage rapide [Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application console Node.js à l’aide de l’identité de l’application](quickstart-v2-nodejs-console.md).

# <a name="python"></a>[Python](#tab/python)

Essayez le démarrage rapide [Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application console Python à l’aide de l’identité de l’application](quickstart-v2-python-daemon.md).

---
