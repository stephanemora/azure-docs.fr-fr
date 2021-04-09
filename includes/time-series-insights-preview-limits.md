---
title: Fichier include
description: Fichier Include
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 016ad0e11f3378dba887e0a235f235fa91e3aa03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98109292"
---
### <a name="property-limits"></a>Limites de propriétés

Les limites de propriété d’Azure Time Series Insights sont passées à 1 000 pour le stockage chaud et à aucune limite de propriété pour le stockage froid. Les propriétés d’événements fournies ont des colonnes JSON, CSV et de graphique que vous pouvez afficher dans l’[Explorateur Azure Time Series Insights Gen2](../articles/time-series-insights/quickstart-explore-tsi.md).

| SKU | Propriétés maximales |
| --- | --- |
| GEN2 (L1) | 1 000 propriétés (colonnes) pour le stockage chaud et illimité pour le stockage froid|
| Gen1 (S1) | 600 propriétés (colonnes) |
| Gen1 (S2) | 800 propriétés (colonnes) |

### <a name="streaming-ingestion"></a>Ingestion de streaming

* Il ne peut y avoir que deux [sources d’événements](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) maximum par environnement.

* Vous pouvez consulter les meilleures pratiques et les recommandations générales pour les sources d’événement [ici](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)

* Par défaut, Azure Time Series Insights Gen2 peut ingérer des données entrantes à un débit allant **jusqu’à 1 mégaoctet par seconde (Mbit/s) par environnement Azure Time Series Insights Gen2**. Il existe des limitations supplémentaires [par partition de hub](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits). Vous pouvez obtenir des taux pouvant atteindre 2 Mbits/s en envoyant un ticket de support via le portail Azure. Pour en savoir plus, consultez [Limites de débit d’ingestion de diffusion en continu](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md).

### <a name="api-limits"></a>Limites de l’API

Les limites de l’API REST pour Azure Time Series Insights Gen2 sont spécifiées dans la [documentation de référence sur l’API REST](/rest/api/time-series-insights/preview#limits-1).