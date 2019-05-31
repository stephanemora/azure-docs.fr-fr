---
title: Authentification à l’aide d’Azure Active Directory dans les clouds nationaux
description: Découvrez l’inscription d’application et les points de terminaison d’authentification pour les clouds nationaux.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: negoe,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd37366697a9c1f5019d2864e6d81a4dcd02e3a2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235488"
---
# <a name="national-clouds"></a>Clouds nationaux

Clouds nationaux sont physiquement isolées les instances d’Azure. Ces régions d’Azure sont conçues pour garantir que les conditions de résidence, de souveraineté et de conformité des données sont respectées au sein de limites géographiques.

Y compris le nuage global, Azure Active Directory (Azure AD) est déployé dans les clouds nationaux suivants :  

- Azure Government
- Azure Germany
- Azure China 21Vianet

Clouds nationaux sont uniques et un environnement distinct à partir d’Azure global. Il est important d’être conscient des différences clés lors du développement de votre application pour ces environnements. Différences incluent l’inscription des applications, l’acquisition de jetons et configuration de points de terminaison.

## <a name="app-registration-endpoints"></a>Points de terminaison d’inscription d’application

Il existe un portail Azure distinct pour chacune des clouds nationaux. Pour intégrer des applications avec la plateforme d’identité Microsoft dans un cloud national, vous devez inscrire votre application séparément dans chaque portail Azure, qui est spécifique à l’environnement.

Le tableau suivant répertorie les URL de base pour les points de terminaison Azure AD utilisés pour inscrire une application pour chaque cloud national.

| Cloud national | Point de terminaison du Portail Azure AD |
|----------------|--------------------------|
| Azure AD pour le gouvernement des États-Unis | `https://portal.azure.us` |
| Azure AD Allemagne | `https://portal.microsoftazure.de` |
| Azure AD Chine géré par 21Vianet | `https://portal.azure.cn` |
| Azure AD (service mondial) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Points de terminaison d’authentification Azure AD

Tous les clouds nationaux authentifient les utilisateurs séparément dans chaque environnement et ont des points de terminaison d’authentification distincts.

Le tableau suivant répertorie les URL de base pour les points de terminaison Azure AD utilisés pour acquérir des jetons pour chaque cloud national.

| Cloud national | Le point de terminaison de l’authentification Azure AD |
|----------------|-------------------------|
| Azure AD pour le gouvernement des États-Unis | `https://login.microsoftonline.us` |
| Azure AD Allemagne| `https://login.microsoftonline.de` |
| Azure AD Chine géré par 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (service mondial)| `https://login.microsoftonline.com` |

Vous pouvez former des requêtes aux points de terminaison de jeton d’autorisation Azure AD à l’aide de l’URL de base spécifiques à une région appropriée. Par exemple, pour Azure Allemagne :

  - Le point de terminaison d’autorisation commun est `https://login.microsoftonline.de/common/oauth2/authorize`.
  - Le point de terminaison de jeton commun est `https://login.microsoftonline.de/common/oauth2/token`.

Pour les applications à locataire unique, remplacez « commun » dans les URL précédentes avec votre nom ou ID de locataire. Par exemple `https://login.microsoftonline.de/contoso.com`.

> [!NOTE]
> Le [v2.0 d’Azure AD]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) et points de terminaison de jeton sont disponibles uniquement pour le service global. Ils ne sont pas pris en charge pour les déploiements de cloud national.

## <a name="microsoft-graph-api"></a>API Microsoft Graph

Pour savoir comment appeler les API Microsoft Graph dans un environnement de cloud national, accédez à [Microsoft Graph dans les déploiements de cloud national](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Certains services et fonctionnalités qui se trouvent dans des régions spécifiques du service global peut-être pas disponibles dans tous les clouds nationaux. Pour savoir quels services sont disponibles, accédez à [des produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Pour savoir comment créer une application à l’aide de la plateforme Microsoft identity, suivez le [didacticiel de la bibliothèque d’authentification Microsoft (MSAL)](msal-national-cloud.md). Plus précisément, cette application sera connecter un utilisateur et obtenir un jeton d’accès pour appeler l’API Microsoft Graph.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Allemagne](https://docs.microsoft.com/azure/germany/)
- [Principes fondamentaux de l’authentification AD Azure](authentication-scenarios.md)
