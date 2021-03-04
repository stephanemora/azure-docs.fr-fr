---
title: Fichier include
description: Fichier Include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 03d83d96bb58ddaf9be4e46c4b62f10e2476e549
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750980"
---
Il existe certaines limites au nombre de métriques et d’événements par application, c’est-à-dire, par clé d’instrumentation. Les limites varient selon le [plan de tarification](https://azure.microsoft.com/pricing/details/application-insights/) que vous choisissez.

| Ressource | Limite | Remarque
| --- | --- | --- |
| Total des données par jour | 100 Go | Vous pouvez réduire les données en définissant une limite. Si vous avez besoin de davantage de données, vous pouvez augmenter la limite dans le portail, jusqu’à 1 000 Go. Pour une capacité supérieure à 1 000 Go, envoyez un e-mail à AIDataCap@microsoft.com.
| Limitation | 32 000 événements/seconde | La limite est mesurée par minute.
| Conservation des données | [30 à 730 jours](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period) | Cette ressource est pour [Recherche](../articles/azure-monitor/app/diagnostic-search.md), [Analytics](../articles/azure-monitor/logs/log-query-overview.md) et [Metrics Explorer](../articles/azure-monitor/essentials/metrics-charts.md).
| Rétention des résultats détaillés du [test de disponibilité à plusieurs étapes](../articles/azure-monitor/app/availability-multistep.md) | 90 jours | Cette ressource fournit des résultats détaillés de chaque étape.
| Taille d’événement maximale | 64 000 octets |
| Longueur des noms de propriétés et de mesures | 150 | Consultez les [schémas par type](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Longueur de chaîne de valeur de propriété | 8 192 | Consultez les [schémas par type](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Longueur des messages de trace et d’exception | 32,768  | Consultez les [schémas par type](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Nombre de [tests de disponibilité](../articles/azure-monitor/app/monitor-web-app-availability.md) par application | 100 |
| Rétention de données [Profileur](../articles/azure-monitor/app/profiler.md) | 5 jours |
| Données [Profileur](../articles/azure-monitor/app/profiler.md) envoyées par jour | 10 Go |

Pour plus d’informations, consultez [Tarification et quotas dans Application Insights](../articles/azure-monitor/app/pricing.md).