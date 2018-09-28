---
title: Authentification à l’aide d’Azure AD dans les clouds nationaux
description: Découvrez l’inscription d’application et les points de terminaison d’authentification pour les clouds nationaux.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda
ms.custom: aaddev
ms.openlocfilehash: 866a86178d66b7b4af069d684e4eb56c12db47ca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981999"
---
# <a name="national-clouds"></a>Clouds nationaux

Les clouds nationaux (également appelé clouds souverains) sont des instances d’Azure physiquement isolées. Ces régions d’Azure sont conçues pour garantir que les conditions de résidence, de souveraineté et de conformité des données sont respectées au sein de limites géographiques.

Outre le cloud global, Azure Active Directory est déployé dans les clouds nationaux suivants :  

- Azure US Government
- Azure Germany
- Azure China 21Vianet

## <a name="app-registration-endpoints"></a>Points de terminaison d’inscription d’application

Le tableau suivant répertorie les URL de base des points de terminaison Azure Active Directory (Azure AD) utilisés pour inscrire une application pour chaque cloud national.

| Cloud national | Point de terminaison du Portail Azure AD
| --- | --- |
| Azure AD pour le gouvernement des États-Unis |https://portal.azure.us
|Azure AD Allemagne |https://portal.microsoftazure.de
|Azure AD Chine géré par 21Vianet |https://portal.azure.cn
|Azure AD (service mondial)|https://portal.azure.com

## <a name="azure-ad-authentication-endpoints"></a>Points de terminaison d’authentification Azure AD

Le tableau suivant répertorie les URL de base des points de terminaison Azure Active Directory (Azure AD) utilisés pour obtenir des jetons afin d’appeler Microsoft Graph pour chaque cloud national.

| Cloud national | Point de terminaison d’authentification Azure AD
| --- | --- |
| Azure AD pour le gouvernement des États-Unis |`https://login.microsoftonline.us`
|Azure AD Allemagne| `https://login.microsoftonline.de`
|Azure AD Chine géré par 21Vianet | `https://login.chinacloudapi.cn`
|Azure AD (service mondial)|`https://login.microsoftonline.com`

Les requêtes adressées aux points de terminaison de jeton ou d’autorisation Azure AD peuvent être formées à l’aide de l’URL de base spécifique à la région appropriée. Par exemple, dans le cas de l’Allemagne :

- Le point de terminaison commun de l’autorisation est `https://login.microsoftonline.de/common/oauth2/authorize`
- Le point de terminaison commun du jeton est `https://login.microsoftonline.de/common/oauth2/token` 

Pour les applications à locataire unique, remplacez common dans les URL ci-dessus par l’ID ou le nom de votre locataire, par exemple, `https://login.microsoftonline.de/contoso.com`

>[!NOTE]
> Les points de terminaison de jeton et [d’autorisation Azure AD v2.0]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) sont disponibles uniquement pour le service mondial. Ils ne sont pas encore pris en charge pour les déploiements de clouds nationaux.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- En savoir plus sur [Azure Chine géré par 21Vianet](https://docs.microsoft.com/azure/china/)
- En savoir plus sur [Azure Allemagne](https://docs.microsoft.com/azure/germany/)
- En savoir plus sur les [principes de base de l’authentification Azure AD](authentication-scenarios.md)
- En savoir plus sur le [déploiement de Microsoft Graph dans un cloud national](https://developer.microsoft.com/graph/docs/concepts/deployments)