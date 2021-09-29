---
title: Surveiller les applications qui s’exécutent sur Azure Functions à l’aide d’Application Insights – Azure Monitor | Microsoft Docs
description: Azure Monitor s’intègre harmonieusement à votre application s’exécutant sur Azure Functions et vous permet de surveiller les performances et de détecter les problèmes de vos applications en un rien de temps.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 08/27/2021
ms.openlocfilehash: ac160463dbfa5fd66afb351c0c0edc5f59b61b30
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124751031"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Surveillance d’Azure Functions avec Application Insights d’Azure Monitor

[Azure Functions](../../azure-functions/functions-overview.md) est intégré à Azure Application Insights pour surveiller des fonctions. Pour les langages autres que .NET et .NETCore, des rôles de travail/extensions supplémentaires spécifiques du langage sont nécessaires pour tirer pleinement parti du suivi distribué. 

Application Insights collecte les données des journaux, des performances et des erreurs et détecte automatiquement les anomalies de performances. Application Insights intègre de puissants outils d’analyse conçus pour aider à diagnostiquer les problèmes et à comprendre la manière dont vos fonctions sont utilisées. Lorsque vous avez la possibilité de visualiser les données de vos applications, vous pouvez améliorer de manière continue les performances et la convivialité. Vous pouvez également utiliser Application Insights lors du développement d'un projet d'application de fonction local. 

L’instrumentation Application Insights nécessaire est intégrée à Azure Functions. La seule chose dont vous avez besoin est une clé d’instrumentation valide pour connecter votre application de fonction à une ressource Application Insights. La clé d’instrumentation doit être ajoutée aux paramètres de votre application lorsque la ressource de votre application de fonction est créée dans Azure. Si votre application de fonction n’a pas encore cette clé, vous pouvez la définir manuellement. Pour plus d’informations, lisez l’article relatif à la[surveillance d’Azure Functions](../../azure-functions/functions-monitoring.md?tabs=cmd).

## <a name="distributed-tracing-for-java-applications-public-preview"></a>Traçage distribué pour les applications Java (préversion publique)

> [!IMPORTANT]
> Cette fonctionnalité est actuellement en préversion publique pour les Azure Functions Java tant Windows que Linux.

Si vos applications sont écrites en Java, vous pouvez afficher des données plus riches à partir de vos applications de fonction, dont les requêtes, les dépendances, les journaux et les métriques. Les données supplémentaires vous permettent également de voir et de diagnostiquer des transactions de bout en bout et de voir la cartographie d’application, qui regroupe de nombreuses transactions afin d’afficher une vue topologique des interactions entre les systèmes et des taux moyens de performance et d’erreur.

Les diagnostics de bout en bout et la cartographie d’application offrent une visibilité sur une transaction/demande unique. Ensemble, ces deux fonctionnalités sont utiles pour trouver la cause racine des problèmes de fiabilité et des goulots d’étranglement des performances par demande.

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Comment activer le suivi distribué pour les applications de fonction Java

Accédez au panneau Vue d’ensemble des applications de fonction, puis à Configurations. Sous Paramètres d’application, cliquez sur « + Nouveau paramètre d’application ». 

> [!div class="mx-imgBorder"]
> ![Sous Paramètres, ajoutez de nouveaux paramètres d’application](./media//functions/create-new-setting.png)

Ajoutez les paramètres d’application suivants avec les valeurs ci-dessous, puis cliquez sur Enregistrer en haut à gauche. C’est terminé !

#### <a name="windows"></a>Windows
```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

#### <a name="linux"></a>Linux
```
ApplicationInsightsAgent_EXTENSION_VERSION -> ~3
```

## <a name="distributed-tracing-for-python-function-apps"></a>Suivi distribué pour applications de fonction Python

Pour collecter des données de télémétrie personnalisées à partir de services tels que Redis, Memcached, MongoDB, etc., vous pouvez utiliser l’[extension Python OpenCensus](https://github.com/census-ecosystem/opencensus-python-extensions-azure) et [journaliser votre télémétrie](../../azure-functions/functions-reference-python.md?tabs=azurecli-linux%2capplication-level#log-custom-telemetry). Vous trouverez la liste des services pris en charge [ici](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib).

## <a name="next-steps"></a>Étapes suivantes

* Lire plus d’instructions et d’informations sur la [surveillance d’Azure Functions](../../azure-functions/functions-monitoring.md)
* Obtenir une vue d’ensemble du [traçage distribué](./distributed-tracing.md)
* Découvrir ce que la [cartographie d’application](./app-map.md?tabs=net) peut apporter à votre entreprise
* En savoir plus sur les [requêtes et les dépendances des applications Java](./java-in-process-agent.md)
* En savoir plus sur [Azure Monitor](../overview.md) et [Application Insights](./app-insights-overview.md)