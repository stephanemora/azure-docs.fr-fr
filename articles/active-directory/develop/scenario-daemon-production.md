---
title: Appel d’application démon web API (passer à la production) - plateforme d’identité Microsoft
description: Découvrez comment créer une application démon par appels web API (passer à la production)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 627dab0cb23800664c5fb5b3df9c61f5071d4b87
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545405"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Application démon qui appelle web API - passer en production

Maintenant que vous savez comment acquérir et utiliser un jeton pour un appel de service à service, découvrez comment déplacer votre application en production.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Déploiement - cas d’applications de démon de l’architecture mutualisée

Si vous êtes un ISV créant une application démon qui peut s’exécuter dans plusieurs clients, vous devez vous assurer que les administrateurs de clients :

- Configure un principal de service pour l’application
- Accorde son consentement à l’application

Vous devez expliquer à vos clients comment effectuer ces opérations. Pour plus d’informations, consultez [demande de consentement d’un client entier](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Étapes suivantes

Voici quelques liens pour en savoir plus :

### <a name="net"></a>.NET

- Si vous n’avez pas déjà le cas, essayez le démarrage rapide [acquérir un jeton et appeler des API Microsoft Graph à partir d’une application console à l’aide d’identité d’application](./quickstart-v2-netcore-daemon.md).
- Documentation de référence pour :
  - L’instanciation [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - Appel [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Autres exemples/didacticiels :
  - [Microsoft-identity-plateforme-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) propose une application de console de démon .NET Core simple qui affiche les utilisateurs d’un locataire interroger Microsoft Graph.

    ![topologie](media/scenario-daemon-app/daemon-app-sample.svg)

    Le même exemple illustre également la variation des certificats.

    ![topologie](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-WebApp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) fonctionnalités d’une application web ASP.NET MVC qui synchronise les données à partir de Microsoft Graph à l’aide de l’identité de l’application au lieu de part d’un utilisateur. L’exemple illustre également le processus de consentement d’administrateur.

    ![topologie](media/scenario-daemon-app/damon-app-sample-web.svg)

### <a name="python"></a>Python

MSAL Python est actuellement en version préliminaire publique. Pour plus d’informations, consultez [MSAL Python client informations d’identification dans référentiel exemple](https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/dev/sample/client_credentials_sample.py).

### <a name="java"></a>Java

MSAL Python est actuellement en version préliminaire publique. Pour plus d’informations, consultez [exemples de référentiel de MSAL Java](https://github.com/AzureAD/azure-activedirectory-library-for-java/tree/dev/src/samples).