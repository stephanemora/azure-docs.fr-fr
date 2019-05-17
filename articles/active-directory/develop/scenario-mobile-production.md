---
title: Applications mobiles que les appels API web (passer à la production) - plateforme d’identité Microsoft
description: Découvrez comment créer une application mobile par appels web API (passer à la production)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc8b7c86495c2a60b07bef0a825e3872f787520
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550397"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Application mobile qui appelle web API - passer en production

Cet article fournit des détails sur la façon d’améliorer la qualité et la fiabilité de votre application avant de le déplacer en production.

## <a name="handling-errors-in-mobile-applications"></a>Gestion des erreurs dans les applications mobiles

Un nombre de conditions d’erreur peut se produire à ce stade dans votre application. Les principaux scénarios pour gérer sont échecs silencieux et les solutions de secours à une interaction. Autres conditions dont vous devez tenir compte pour la production incluent les situations sans réseau, interruptions de service, configuration requise pour le consentement de l’administrateur et autres cas spécifiques au scénario.

Chaque bibliothèque MSAL présente un exemple de code et wiki contenu décrivant comment gérer ces conditions :

- [Wiki Android MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [Wiki de MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Atténuation et examen des problèmes

Pour diagnostiquer les problèmes dans votre application, il permet de collecter des données. Pour plus d’informations sur les types de données, vous pouvez collecter, consultez les wikis de plateforme MSAL.

- Les utilisateurs peuvent demander de l’aide lorsqu’ils rencontrent des problèmes. Une bonne pratique consiste à capturer et temporairement stocker les journaux et fournir un emplacement où les utilisateurs peuvent les télécharger. MSAL fournit des extensions de journalisation pour capturer des informations détaillées sur l’authentification.
- S’il est disponible, activer la télémétrie via MSAL pour collecter des données sur la façon dont les utilisateurs sont connectent à votre application.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
