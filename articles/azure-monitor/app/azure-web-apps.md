---
title: Analyser les performances d’Azure App Service | Microsoft Docs
description: Analyse des performances des applications pour les services d’application Azure. Analysez la charge, le temps de réponse et les dépendances dans des graphiques, et définissez des alertes sur les performances.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 03ee74851f82fd8ff7833fc5d27c901cf0b2317a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788740"
---
# <a name="application-monitoring-for-azure-app-service-overview"></a>Vue d’ensemble de la surveillance des applications pour Azure App Service

L’activation de la surveillance de vos applications web ASP.NET, ASP.NET Core, Java et Node.js s’exécutant sur [Azure App Service](../../app-service/index.yml) n’a jamais été aussi facile. Alors qu’auparavant vous deviez instrumenter manuellement votre application, la dernière version de l’agent/extension est désormais intégrée à l’image App Service par défaut. 

## <a name="enable-application-insights"></a>Activer Application Insights

Il existe deux façons d’activer la supervision des applications hébergées par Azure App Services :

- La **supervision basée sur un agent de l’application** (ApplicationInsightsAgent). 
 
    - Cette méthode est la plus facile à activer, et aucune modification du code ni aucune configuration avancée n’est requise. Elle est souvent appelée supervision « runtime ». Pour Azure App Services, nous vous conseillons d’activer ce niveau de supervision au minimum. Ensuite, selon votre scénario spécifique, vous pouvez évaluer si une supervision plus avancée par une instrumentation manuelle est nécessaire.

    - Les langages suivants sont pris en charge pour la supervision basée sur un agent :
        - [.NET Core](./azure-web-apps-net-core.md)
        - [.NET](./azure-web-apps-net.md)
        - [Java](./azure-web-apps-java.md)
        - [Nodejs](./azure-web-apps-nodejs.md)
    
* L’**instrumentation manuelle de l’application avec le code** en installant le SDK Application Insights.

    * Cette approche est beaucoup plus personnalisable, mais requiert les ressources suivantes : kits de développement logiciel (SDK) pour [.NET Core](./asp-net-core.md), [.NET](./asp-net.md), [Node.js](./nodejs.md) et [Python](./opencensus-python.md), ainsi qu’un agent autonome pour [Java](./java-in-process-agent.md). Cette méthode implique également de gérer vous-même l’installation des mises à jour vers la dernière version des packages.

    * Utilisez cette méthode si vous devez effectuer des appels d’API personnalisés pour suivre les événements/dépendances qui ne sont pas capturés par défaut avec la supervision basée sur un agent. Pour en savoir plus, consultez l’[article sur l’API Application Insights pour les événements et mesures personnalisés](./api-custom-events-metrics.md). 

> [!NOTE]
> Si une surveillance basée sur un agent et une instrumentation manuelle basée sur un kit de développement logiciel (SDK) sont toutes deux détectées, dans .NET, seuls les paramètres d’instrumentation manuelle sont respectés, tandis que, dans Java, seule l’instrumentation basée sur un agent émet la télémétrie. Cela évite que des données en double soient envoyées.

> [!NOTE]
> Le débogueur de capture instantanée et le profileur ne sont disponibles que dans .NET et .NET Core

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment activer la supervision des applications basées sur un agent pour votre application [.NET Core](./azure-web-apps-net-core.md), [.NET](./azure-web-apps-net.md), [Java](./azure-web-apps-java.md) ou [Node.js](./azure-web-apps-nodejs.md) exécuté sur App Service. 
