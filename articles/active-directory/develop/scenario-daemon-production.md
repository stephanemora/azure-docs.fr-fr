---
title: Passer en production une application démon appelant des API web – Plateforme d’identités Microsoft | Azure
description: Découvrir comment passer en production une application démon qui appelle des API web
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 961928499008445207df3a34a51fc016723c294b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962591"
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

Essayez le démarrage rapide [Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application console Python à l’aide de l’identité de l’application](./quickstart-v2-python-daemon.md).

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java est actuellement en préversion publique. Pour plus d’informations, voir [Exemples de développement MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
