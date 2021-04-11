---
title: Préparer des API web appelant des applications mobiles pour la production | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment générer une application mobile qui appelle des API web. (Préparer des applications pour la production.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 08243fd06de289941d8e6a9197ccb349614af056
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675955"
---
# <a name="prepare-mobile-apps-for-production"></a>Préparer des applications mobiles pour la production

Cet article fournit des détails sur la façon d’améliorer la qualité et la fiabilité de votre application mobile avant de la mettre en production.

## <a name="handle-errors"></a>des erreurs

Lorsque vous préparez une application mobile pour la production, plusieurs conditions d’erreur peuvent se produire. Les principaux cas que vous allez gérer sont des échecs silencieux et des solutions de secours pour l’interaction. Vous devez aussi tenir compte des situations sans réseau, des interruptions de service, de la configuration requise pour le consentement administrateur et d’autres cas spécifiques du scénario.

Pour chaque type de Bibliothèque d’authentification Microsoft (MSAL), vous pouvez trouver des exemples de code et du contenu wiki décrivant comment gérer les conditions d’erreur :

- [Wiki Android MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Wiki iOS MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [Wiki MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)


[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour essayer des exemples supplémentaires, voir [Applications clientes publiques mobiles et de bureau](sample-v2-code.md#desktop-and-mobile-public-client-apps).