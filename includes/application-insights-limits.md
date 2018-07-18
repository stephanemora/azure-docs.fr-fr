---
title: Fichier Include
description: Fichier Include
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 06/21/2018
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 90de751f416ca611f3c674232c224199ad7af717
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310165"
---
Il existe certaines limites au nombre de mesures et d’événements par application (c'est-à-dire, par clé d'instrumentation). Les limites varient selon le [plan de tarification](https://azure.microsoft.com/pricing/details/application-insights/) que vous choisissez.

| Ressource | Limite par défaut | Remarque
| --- | --- | --- |
| Total des données par jour | 100 Go | Vous pouvez réduire les données en définissant une limite. Si vous avez besoin de davantage de données, vous pouvez augmenter la limite dans le portail, jusqu’à 1 000 Go. Pour une capacité supérieure à 1 000 Go, envoyez un e-mail à AIDataCap@microsoft.com.
| Limitation | 32 000 événements par seconde | La limite est mesurée par minute.
| Conservation des données | 90 jours | Cette ressource est pour [Recherche](../articles/application-insights/app-insights-diagnostic-search.md), [Analytics](../articles/application-insights/app-insights-analytics.md) et [Metrics Explorer](../articles/application-insights/app-insights-metrics-explorer.md).
| Rétention des résultats détaillés du [test de disponibilité à plusieurs étapes](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) | 90 jours | Cette ressource fournit des résultats détaillés de chaque étape.
| Taille d’événement maximale | 64 K | 
| Longueur des noms de propriétés et de mesures | 150 | Consultez les [schémas par type](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Longueur de chaîne de valeur de propriété | 8 192 | Consultez les [schémas par type](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Longueur des messages de trace et d’exception | 10 000 | Consultez les [schémas par type](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Nombre de [tests de disponibilité](../articles/application-insights/app-insights-monitor-web-app-availability.md) par application | 100 |
| Rétention de données [Profileur](../articles/application-insights/app-insights-profiler.md) | 5 jours |
| Données [Profileur](../articles/application-insights/app-insights-profiler.md) envoyées par jour | 10 Go |

Pour plus d’informations, consultez [Tarification et quotas dans Application Insights](../articles/application-insights/app-insights-pricing.md).