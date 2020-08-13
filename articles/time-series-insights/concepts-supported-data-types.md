---
title: Types de données pris en charge – Azure Time Series Insights Gen2 | Microsoft Docs
description: Découvrez les types de données pris en charge dans Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: e6fd405d1969a2f40a5f0c3466a57fbec60723e9
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141157"
---
# <a name="supported-data-types"></a>Types de données pris en charge

Le tableau suivant répertorie les types de données qu’Azure Time Series Insights Gen2 prend en charge

| Type de données | Description | Exemple | Nom de la colonne de propriété dans Parquet
|---|---|---|---|
| **bool** | Type de données ayant l’un des deux états suivants : `true` ou `false`. | `"isQuestionable" : true` | isQuestionable_bool
| **datetime** | Représente un instant, généralement exprimé sous la forme d’une date ou d’une heure. Valeur exprimée au format [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html). Les propriétés DateHeure sont toujours stockées au format UTC. Les décalages de fuseau horaire, s’ils sont correctement mis en forme, sont appliqués, puis la valeur stockée au format UTC. Pour plus d’informations sur la propriété horodateur de l’environnement et les décalages DateHeure, consultez [cette](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) section | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` | eventProcessedLocalTime_datetime
| **double** | 64 bits double précision  | `"value": 31.0482941` | value_double
| **long** | Entier 64 bits signé  | `"value" : 31` | value_long
| **string** | Les valeurs de texte doivent être au format UTF-8 valide. Les chaînes Null et vides sont traitées de la même façon. |  `"site": "DIM_MLGGG"` | site_string
| **dynamic** | Type complexe (non primitif) constitué d’un tableau ou d’un conteneur de propriétés (dictionnaire). À l’heure actuelle, seuls des tableaux JSON convertis de primitives ou des tableaux d’objets ne contenant pas l’ID TS ou les propriétés d’horodatage sont stockés comme dynamiques. Pour comprendre la façon dont les objets sont aplatis et les tableaux peuvent être déroulés, lisez cet [article](./concepts-json-flattening-escaping-rules.md) . Les propriétés de charge utile stockées comme ce type sont accessibles par le biais de l’Explorateur Azure Time Series Insights Gen2 et de l’API de requête  `GetEvents`. |  `"values": "[197, 194, 189, 188]"` | values_dynamic

## <a name="sending-mixed-data-types"></a>Envoi de types de données mixtes

Votre environnement Azure Time Series Insights Gen2 est très typé. Si des appareils ou des balises envoient des données de types différents pour une propriété d’appareil, les valeurs sont stockées dans deux colonnes séparées, et la fonction [coalesce()](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions) doit être utilisée lors de la définition de vos expressions de variable de modèle de série chronologique dans les appels d’API.

L’Explorateur Azure Time Series Insights offre un moyen de fusionner automatiquement les colonnes distinctes de la même propriété d’appareil. Dans l’exemple ci-dessous, le capteur envoie une propriété `PresentValue` qui peut être une valeur Long ou Double. Pour effectuer une requête sur toutes les valeurs stockées (quel que soit le type de données) de la propriété `PresentValue`, choisissez `PresentValue (Double | Long)` et les colonnes seront fusionnées pour vous.

[![Fusion automatique d’explorateur](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Objets et tableaux

Vous pouvez envoyer des types complexes tels que des objets et des tableaux dans le cadre de votre charge utile d’événement. Les objets imbriqués sont aplatis et les tableaux sont stockés comme `dynamic` ou aplatis pour produire plusieurs événements en fonction de la configuration de votre environnement et de la forme JSON. Pour en savoir plus, consultez les [Règles de mise à plat et d’échappement de JSON](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Étapes suivantes

* Lisez les [Règles de mise à plat et d’échappement JSON](./concepts-json-flattening-escaping-rules.md) pour comprendre comment les événements seront stockés.

* Comprendre les [limites de débit](./concepts-streaming-ingress-throughput-limits.md) de votre environnement

* En savoir plus sur les [sources d’événements](concepts-streaming-ingestion-event-sources.md) pour ingérer les données de diffusion en continu.
