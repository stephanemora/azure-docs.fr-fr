---
title: Fichier Include
description: Fichier Include
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: f097479b67a7b48eca4a2710db3bd7eed6ddc982
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "77013783"
---
La section suivante résume les limites de clé en disponibilité générale.

### <a name="sku-ingress-rates-and-capacities"></a>Capacités et débits d’entrée de la référence (SKU)

Les vitesses et capacités d’entrée des références (SKU) S1 et S2 offrent une certaine souplesse lors de la configuration d’un nouvel environnement Azure Time Series Insights.

| Capacité de la référence (SKU) S1 | Débit d’entrée | Capacité de stockage maximale
| --- | --- | --- |
| 1 | 1 Go (1 million d’événements) | 30 Go (30 millions d’événements) par mois |
| 10 | 10 Go (10 millions d’événements) | 300 Go (300 millions d’événements) par mois |

| Capacité de la référence (SKU) S2 | Débit d’entrée | Capacité de stockage maximale
| --- | --- | --- |
| 1 | 10 Go (10 millions d’événements) | 300 Go (300 millions d’événements) par mois |
| 10 | 100 Go (100 millions d’événements) | 3 To (3 milliards d’événements) par mois |

> [!NOTE]
> Les capacités sont mises à l’échelle de façon linéaire. Par conséquent, une référence SKU S1 avec la capacité 2 prend en charge 2 Go (2 millions) d’événements par débit d’entrée par jour et 60 Go (60 millions d’événements) par mois.

Les environnements de référence (SKU) S2 prennent en charge beaucoup plus d’événements par mois et ont une capacité d’entrée sensiblement supérieure.

| SKU  | Nombre d’événements par mois  | Taille d’événement par mois  | Nombre d’événements par minute | Taille d’événement par minute  |
|---------|---------|---------|---------|---------|
| S1     |   30 millions     |  30 Go     |  720    |  720 Ko   |
 |S2     |   300 millions    |   300 Go   | 7 200   | 7 200 Ko  |

### <a name="property-limits"></a>Limites de propriétés

Les limites de propriétés en disponibilité générale dépendent de l’environnement de référence (SKU) sélectionné. Les propriétés d’événements fournies ont des colonnes JSON, CSV et de graphique qui peuvent être affichées dans l’[Explorateur Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| SKU | Propriétés maximales |
| --- | --- |
| S1 | 600 propriétés (colonnes) |
| S2 | 800 propriétés (colonnes) |

### <a name="event-sources"></a>Sources d’événement

Au maximum deux sources d’événements par instance sont prise en charge. 

* Découvrez comment [ajouter une source Event Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Configurez [une source de hub IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>Limites de l’API

Les limites de l’API REST pour Time Series Insights en disponibilité générale sont spécifiées dans la [documentation de référence sur l’API REST](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits).