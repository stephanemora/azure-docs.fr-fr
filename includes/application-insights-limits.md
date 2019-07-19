---
title: Fichier Include
description: Fichier Include
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 06/20/2019
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 708421feca879ade26a1488541fa7fbf8a601358
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303821"
---
Il existe certaines limites au nombre de métriques et d’événements par application, c’est-à-dire, par clé d’instrumentation. Les limites varient selon le [plan de tarification](https://azure.microsoft.com/pricing/details/application-insights/) que vous choisissez.

| Ressource | Limite par défaut | Remarque
| --- | --- | --- |
| Total des données par jour | 100 Go | Vous pouvez réduire les données en définissant une limite. Si vous avez besoin de davantage de données, vous pouvez augmenter la limite dans le portail, jusqu’à 1 000 Go. Pour une capacité supérieure à 1 000 Go, envoyez un e-mail à AIDataCap@microsoft.com.
| Limitation | 32 000 événements/seconde | La limite est mesurée par minute.
| Conservation des données | 90 jours | Cette ressource est pour [Recherche](../articles/azure-monitor/app/diagnostic-search.md), [Analytics](../articles/azure-monitor/app/analytics.md) et [Metrics Explorer](../articles/azure-monitor/app/metrics-explorer.md).
| Rétention des résultats détaillés du [test de disponibilité à plusieurs étapes](../articles/azure-monitor/app/availability-multistep.md) | 90 jours | Cette ressource fournit des résultats détaillés de chaque étape.
| Taille d’événement maximale | 64 000 |
| Longueur des noms de propriétés et de mesures | 150 | Consultez les [schémas par type](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Longueur de chaîne de valeur de propriété | 8 192 | Consultez les [schémas par type](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Longueur des messages de trace et d’exception | 32 768  | Consultez les [schémas par type](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Nombre de [tests de disponibilité](../articles/azure-monitor/app/monitor-web-app-availability.md) par application | 100 |
| Rétention de données [Profileur](../articles/azure-monitor/app/profiler.md) | 5 jours |
| Données [Profileur](../articles/azure-monitor/app/profiler.md) envoyées par jour | 10 Go |

Pour plus d’informations, consultez [Tarification et quotas dans Application Insights](../articles/azure-monitor/app/pricing.md).