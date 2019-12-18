---
title: Passer en production une application mobile appelant des API web - Plateforme d’identités Microsoft | Azure
description: Apprenez à générer une application mobile qui appelle des API web (passage en production)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e267b2f398480da6949a4c85133342aad5c2ba7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919900"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Application mobile appelant des API web : passage en production

Cet article fournit des détails sur la façon d’améliorer la qualité et la fiabilité de votre application avant de la passer en production.

## <a name="handling-errors-in-mobile-applications"></a>Gestion des erreurs dans les applications mobiles

Certaines conditions d’erreur peut se produire à ce stade dans votre application. Les principaux scénarios à gérer sont des échecs silencieux et des solutions de secours pour l’interaction. Vous devez aussi tenir compte des situations sans réseau, des interruptions de service, de la configuration requise pour le consentement administrateur et d’autres cas spécifiques en fonction du scénario.

Chaque bibliothèque MSAL présente un exemple de code et du contenu wiki décrivant comment gérer ces conditions :

- [Wiki Android MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Wiki iOS MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [Wiki MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Limitation et étude des problèmes

Pour diagnostiquer les problèmes dans votre application, il est intéressant de collecter des données. Pour plus d’informations sur les types de données à collecter, consultez les wikis de plateforme MSAL.

- Les utilisateurs peuvent demander de l’aide lorsqu’ils rencontrent des problèmes. Une bonne pratique consiste à capturer, stocker temporairement les journaux et fournir un emplacement où les utilisateurs peuvent les charger. MSAL fournit des extensions de journalisation permettant de capturer des informations détaillées sur l’authentification.
- Le cas échéant, activez la télémétrie par le biais de MSAL pour collecter des données sur la manière dont les utilisateurs se connectent à votre application.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

Essayer des exemples supplémentaires disponibles à partir d’[Exemples | Applications clientes de bureau et mobiles publiques](sample-v2-code.md#desktop-and-mobile-public-client-apps)
