---
title: Applications mobiles que les appels API web (passer à la production) - plateforme d’identité Microsoft
description: Découvrez comment créer une application mobile qui appelle l’API Web (passer à la production)
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
ms.openlocfilehash: d37d2de561a6f5841bf17a47fef86ad7639750d5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074949"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Application mobile qui appelle web API - passer en production

Cet article fournit des détails sur l’amélioration de la qualité et la fiabilité de votre application pour le déplacer vers la production.

## <a name="handling-errors-in-mobile-applications"></a>Gestion des erreurs dans les applications mobiles

Dans les différents flux que nous avons mis en surbrillance jusqu'à présent, il existe une variété de conditions d’erreur qui peut être provoquée. Le scénario principal pour gérer est échecs silencieux et secours à une interaction. Il existe des conditions supplémentaires, que vous devez également envisager pour la production, y compris aucun réseau situations, les interruptions de service, consentement de l’administrateur requis et autres cas spécifiques au scénario.

Chaque bibliothèque MSAL a un exemple de code et wiki contenu qui explore plus approfondie gestion de ces conditions.

- [Wiki Android MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [Wiki de MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Atténuation et examen des problèmes

Collecte de données aidera votre application à diagnostiquer les problèmes. Pour plus d’informations sur le type de données, vous pouvez collecter, consultez chaque wiki de plateformes MSAL.

- Les utilisateurs peuvent demander de l’aide si vous rencontrez un problème. Une bonne pratique consiste à capturer et stocker temporairement les journaux et permettre aux utilisateurs de télécharger les quelque part. MSAL fournit des extensions de journalisation pour capturer des informations détaillées sur l’authentification.
- S’il est disponible, activer la télémétrie via MSAL pour collecter des données sur la façon dont les utilisateurs se connectent à votre application.

## <a name="testing-your-app"></a>Test de votre application

Veillez à tester votre application avec le [liste de vérification de l’intégration](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
