---
title: Utilisation d’Azure Monitor Metrics avec Azure Functions
description: Découvrez comment utiliser Azure Monitor Metrics pour afficher et interroger les données de télémétrie d’Azure Functions collectées et stockées par Azure Application Insights.
ms.topic: how-to
ms.date: 07/4/2021
ms.openlocfilehash: b5f0b483295cccff8caf97503233e1e7881173cf
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568597"
---
# <a name="using-azure-monitor-metric-with-azure-functions"></a>Utilisation d’Azure Monitor Metrics avec Azure Functions

Azure Functions s’intègre à Azure Monitor Metrics pour vous permettre d’analyser les métriques générées par votre application de fonction au cours de son exécution. Pour en savoir plus, consultez [Vue d’ensemble d’Azure Monitor Metrics](../azure-monitor/essentials/data-platform-metrics.md). Ces métriques indiquent comment votre application de fonction s’exécute sur la plateforme App Service. Vous pouvez consulter les données de consommation de ressources utilisées pour estimer les coûts du plan de consommation. Pour étudier la télémétrie détaillée de vos exécutions de fonction, y compris les données de journal, vous devez également utiliser [Application Insights](functions-monitoring.md) dans Azure Monitor. 

> [!NOTE]
> La fonctionnalité Azure Monitor Metrics n’est pas prise en charge actuellement lorsque votre application de fonction s’exécute sur Linux dans un plan de consommation.

## <a name="available-metrics"></a>Métriques disponibles

Azure Monitor collecte des données numériques à partir d’un ensemble de ressources supervisées, qui sont entrées dans une base de données de série chronologique. Azure Monitor collecte les métriques spécifiques à Functions et aux ressources App Service sous-jacentes.   

### <a name="functions-specific-metrics"></a>Métriques spécifiques à Functions

Deux métriques spécifiques à Functions présentent un intérêt :

| Métrique | Description |
| ---- | ---- |
| **FunctionExecutionCount** | Le nombre d’exécutions de fonction indique le nombre de fois que votre application de fonction s’est exécutée. Cette valeur se rapporte au nombre de fois qu’une fonction s’exécute dans votre application. |
| **FunctionExecutionUnits** | Les unités d’exécution de fonction sont une combinaison du temps d’exécution et de votre utilisation de la mémoire.  La métrique des données de mémoire n’est actuellement pas disponible par le biais d’Azure Monitor. En revanche, si vous voulez optimiser l’utilisation de la mémoire de votre application, vous pouvez utiliser les données du compteur de performances collectées par Application Insights. Actuellement, cette métrique n’est pas prise en charge pour les plans Premium et Dedicated (App Service) s’exécutant sur Linux.|

Ces métriques sont utilisées spécifiquement lors de l’[estimation des coûts du plan de consommation](functions-consumption-costs.md). 

### <a name="general-app-service-metrics"></a>Métriques App Service générales

En plus des métriques spécifiques aux fonctions, la plateforme App Service implémente d’autres métriques, que vous pouvez utiliser pour surveiller les applications de fonction. Pour obtenir la liste complète, consultez les [métriques disponibles pour les applications App Service](../app-service/web-sites-monitor.md#understand-metrics).

## <a name="accessing-metrics"></a>Accès aux métriques

Vous pouvez utiliser [Azure Monitor Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) dans le [portail Azure](https://portal.azure.com) ou les API REST pour obtenir ces données Monitor Metrics. 

Les exemples suivants utilisent Monitor Metrics pour estimer le coût de l’exécution de votre application de fonction sur un plan de consommation. Pour en savoir plus sur l’estimation des coûts du plan de consommation, consultez [Estimation des coûts d’un plan de consommation](functions-consumption-costs.md).

[!INCLUDE [functions-monitor-metrics-consumption](../../includes/functions-monitor-metrics-consumption.md)]  

Pour en savoir plus sur l’utilisation de l’explorateur de surveillance pour afficher des métriques, consultez [Prise en main d’Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la supervision dans Azure Functions :

+ [Superviser Azure Functions](functions-monitoring.md)
+ [Analyser la télémétrie d’Azure Functions dans Application Insights](analyze-telemetry-data.md)
