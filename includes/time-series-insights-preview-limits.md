---
title: Fichier Include
description: Fichier Include
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 7259e1981f873c8385a02fe4f353dcdda495f823
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91287409"
---
### <a name="property-limits"></a>Limites de propriétés

Les limites de propriétés Azure Time Series Insights ont augmenté jusqu’à 1 000 à partir d’un plafond maximal de 800 en Gen1. Les propriétés d’événements fournies ont des colonnes JSON, CSV et de graphique que vous pouvez afficher dans l’[Explorateur Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Propriétés maximales |
| --- | --- |
| GEN2 (L1) | 1 000 propriétés (colonnes) |
| Gen1 (S1) | 600 propriétés (colonnes) |
| Gen1 (S2) | 800 propriétés (colonnes) |

### <a name="streaming-ingestion"></a>Ingestion de streaming

* Il ne peut y avoir que deux [sources d’événements](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) maximum par environnement.

* Vous pouvez consulter les meilleures pratiques et les recommandations générales pour les sources d’événement [ici](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)

* Par défaut, Azure Time Series Insights Gen2 peut ingérer des données entrantes à un débit allant **jusqu’à 1 mégaoctet par seconde (Mbit/s) par environnement Azure Time Series Insights Gen2**. Il existe des limitations supplémentaires [par partition de hub](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits). Vous pouvez fournir des taux pouvant atteindre 8 Mbits/s en soumettant un ticket de support via le portail Azure. Pour en savoir plus, consultez [Limites de débit d’ingestion de diffusion en continu](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md).

### <a name="api-limits"></a>Limites de l’API

Les limites de l’API REST pour Azure Time Series Insights Gen2 sont spécifiées dans la [documentation de référence sur l’API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1).
