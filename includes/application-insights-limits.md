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
ms.openlocfilehash: 31ff6ae66b13eb5e5abcde94e8d3df7f6e32f63c
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407853"
---
Il existe certaines limites sur le nombre de mesures et les événements par application, autrement dit, chaque clé d’instrumentation. Les limites varient selon le [plan de tarification](https://azure.microsoft.com/pricing/details/application-insights/) que vous choisissez.

| Ressource | Limite par défaut | Remarque
| --- | --- | --- |
| Total des données par jour | 100 Go | Vous pouvez réduire les données en définissant une limite. Si vous avez besoin de davantage de données, vous pouvez augmenter la limite dans le portail, jusqu’à 1 000 Go. Pour une capacité supérieure à 1 000 Go, envoyez un e-mail à AIDataCap@microsoft.com.
| Limitation | 32 000 événements par seconde | La limite est mesurée par minute.
| Conservation des données | 90 jours | Cette ressource est pour [Recherche](../articles/azure-monitor/app/diagnostic-search.md), [Analytics](../articles/azure-monitor/app/analytics.md) et [Metrics Explorer](../articles/azure-monitor/app/metrics-explorer.md).
| Rétention des résultats détaillés du [test de disponibilité à plusieurs étapes](../articles/azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) | 90 jours | Cette ressource fournit des résultats détaillés de chaque étape.
| Taille d’événement maximale | 64 000 |
| Longueur des noms de propriétés et de mesures | 150 | Consultez les [schémas par type](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Longueur de chaîne de valeur de propriété | 8 192 | Consultez les [schémas par type](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Longueur des messages de trace et d’exception | 32 768  | Consultez les [schémas par type](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Nombre de [tests de disponibilité](../articles/azure-monitor/app/monitor-web-app-availability.md) par application | 100 |
| Rétention de données [Profileur](../articles/azure-monitor/app/profiler.md) | 5 jours |
| Données [Profileur](../articles/azure-monitor/app/profiler.md) envoyées par jour | 10 Go |

Pour plus d’informations, consultez [Tarification et quotas dans Application Insights](../articles/azure-monitor/app/pricing.md).