---
title: 'Types de données pris en charge : Azure Time Series Insights | Microsoft Docs'
description: Découvrez les types de données pris en charge dans Azure Time Series Insights (préversion).
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: 1a1ab9fa19956341e930c85d13d7067be4449cea
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049364"
---
# <a name="supported-data-types"></a>Types de données pris en charge

Le tableau suivant répertorie les types de données pris en charge par Time Series Insights

| Type de données | Description | Exemple | Nom de la colonne de propriété dans Parquet
|---|---|---|---|
| **bool** | Type de données ayant l’un des deux états suivants : `true` ou `false`. | "isQuestionable" : true | isQuestionable_bool
| **datetime** | Représente un instant, généralement exprimé sous la forme d’une date ou d’une heure. Valeur exprimée au format [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html). Les propriétés DateHeure sont toujours stockées au format UTC. Les décalages de fuseau horaire, s’ils sont correctement mis en forme, sont appliqués, puis la valeur stockée au format UTC. Pour plus d’informations sur la propriété horodateur de l’environnement et les décalages DateHeure, consultez [cette](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) section | « eventProcessedLocalTime » : « 2020-03-20T09:03:32.8301668Z » | eventProcessedLocalTime_datetime 
| **double** | 64 bits double précision  | « valeur » : 31.0482941 | value_double
| **long** | Entier 64 bits signé  | « valeur » : 31 | value_long
| **string** | Les valeurs de texte doivent être au format UTF-8 valide. |  « site » : « DIM_MLGGG » | site_string
| **dynamic** | Type complexe (non primitif) constitué d’un tableau ou d’un conteneur de propriétés (dictionnaire). À l’heure actuelle, seuls les tableaux JSON convertis de primitives ou de tableaux d’objets ne contenant pas l’ID TS ou les propriétés d’horodatage sont stockés comme dynamiques. Lisez cet [article](./concepts-json-flattening-escaping-rules.md) pour comprendre la façon dont les objets sont aplatis et les tableaux peuvent être annulés |  « valeurs » : « [197, 194, 189, 188] » | values_dynamic

> [!IMPORTANT]
>
> * Votre environnement TSI est fortement typé. Si des appareils ou des balises envoient des données intégrales et non intégrales, les valeurs des propriétés de l’appareil sont stockées dans deux colonnes séparées (double et long) et la [fonction coalesce()](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) doit être utilisée lors des appels d’API et de la définition de vos expressions de variable de modèle de série chronologique.

#### <a name="objects-and-arrays"></a>Objets et tableaux

Vous pouvez envoyer des types complexes tels que des objets et des tableaux dans le cadre de votre charge utile d’événement. Les objets imbriqués sont aplatis et les tableaux sont stockés comme `dynamic` ou aplatis pour produire plusieurs événements en fonction de la configuration de votre environnement et de la forme JSON. Pour en savoir plus, consultez les [Règles de mise à plat et d’échappement de JSON](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Étapes suivantes

* Lisez les [Règles de mise à plat et d’échappement JSON](./concepts-json-flattening-escaping-rules.md) pour comprendre comment les événements seront stockés. 

* Comprendre les [limites de débit](concepts-streaming-throughput-limitations.md) de votre environnement

* En savoir plus sur les [sources d’événements](concepts-streaming-ingestion-event-sources.md) pour ingérer les données de diffusion en continu.
