---
title: Fichier include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: ae9b749f62dc74a6cb0dfea3701fd4ebac11c188
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107931779"
---
## <a name="enable-logging"></a>Activation de la journalisation

Pour faciliter le débogage et les scénarios de résolution des problèmes d’authentification, la bibliothèque d’authentification Microsoft offre une prise en charge intégrée de la journalisation. La journalisation pour chaque bibliothèque est couverte dans les articles suivants :

:::row:::
    :::column:::
        - [Journalisation dans MSAL.NET](../articles/active-directory/develop/msal-logging-dotnet.md)
        - [Journalisation dans MSAL pour Android](../articles/active-directory/develop/msal-logging-android.md)
        - [Journalisation dans MSAL.js](../articles/active-directory/develop/msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [Journalisation dans MSAL pour iOS/macOS](../articles/active-directory/develop/msal-logging-ios.md)
        - [Journalisation dans MSAL pour Java](../articles/active-directory/develop/msal-logging-java.md)
        - [Journalisation dans MSAL pour Python](../articles/active-directory/develop/msal-logging-python.md)
    :::column-end:::
:::row-end:::

Voici quelques suggestions pour la collecte de données :

- Les utilisateurs peuvent demander de l’aide lorsqu’ils rencontrent des problèmes. Une meilleure pratique consiste à capturer et à stocker temporairement les journaux. Indiquez un emplacement où les utilisateurs peuvent charger les journaux. MSAL fournit des extensions de journalisation permettant de capturer des informations détaillées sur l’authentification.

- Si une télémétrie est disponible, activez-la via MSAL pour recueillir des données sur la façon dont les utilisateurs se connectent à votre application.


## <a name="validate-your-integration"></a>Valider votre intégration

Testez votre intégration en suivant la [check-list de l’intégration à la plateforme d’identités Microsoft](../articles/active-directory/develop/identity-platform-integration-checklist.md).

## <a name="build-for-resilience"></a>Créer pour la résilience

Découvrez comment augmenter la résilience dans votre application. Pour plus de détails, consultez [Augmenter la résilience des applications d’authentification et d’autorisation que vous développez](../articles/active-directory/fundamentals/resilience-app-development-overview.md)
