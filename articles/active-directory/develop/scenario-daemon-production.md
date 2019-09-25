---
title: 'Application démon appelant des API web (passage en production) : plateforme d’identités Microsoft'
description: Apprenez à générer une application démon qui appelle des API web (passage en production)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c509e061c43c81f72682fb428529a8e72b34066a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056323"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Application démon appelant des API web : passage en production

Maintenant que vous savez comment obtenir et utiliser un jeton pour un appel de service à service, découvrez comment faire passer votre application en production.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Déploiement : cas d’applications démon multi-locataires

Si vous êtes un éditeur de logiciels indépendant (ISV) créant une application démon qui peut s’exécuter dans plusieurs locataires, vous devez vous assurer que les administrateurs de locataires :

- Approvisionnent un principal de service pour l’application
- Accordent les autorisations de l’application

Vous devrez expliquer à vos clients comment effectuer ces opérations. Pour plus d’informations, consultez [Demande de consentement d’un client entier](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Étapes suivantes

Voici quelques liens pour en savoir plus :

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

- Si vous ne l’avez pas déjà fait, essayez le démarrage rapide [Obtenir un jeton d’accès et appeler l’API Microsoft Graph à partir d’une application console à l’aide de l’identité de l’application](./quickstart-v2-netcore-daemon.md).
- Documentation de référence pour :
  - Instanciation de [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - Appeler [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Autres exemples/tutoriels :
  - [microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) propose une simple application de console démon .NET Core qui affiche les utilisateurs d’un locataire interrogeant Microsoft Graph.

    ![Topologie](media/scenario-daemon-app/daemon-app-sample.svg)

    Le même exemple illustre également la variation avec des certificats.

    ![Topologie](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) propose une application web ASP.NET MVC qui synchronise les données Microsoft Graph en utilisant l’identité de l’application plutôt que le compte d’un utilisateur. L’exemple illustre également le processus de consentement administrateur.

    ![Topologie](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="pythontabpython"></a>[Python](#tab/python)

MSAL Python est disponible en préversion publique.
Pour plus d’informations, consultez [Exemples MSAL Python dans le dépôt](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample).

# <a name="javatabjava"></a>[Java](#tab/java)

msal4j (MSAL.Java) est actuellement en préversion publique. Pour plus d’informations, consultez [Exemples MSAL Java dans le référentiel](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
