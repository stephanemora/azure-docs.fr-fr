---
title: Mettre en forme des événements - Azure Time Series Insights | Microsoft Docs
description: Découvrez les meilleures pratiques et le façonnage d’événements pour l’interrogation dans Azure Time Insights (préversion).
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 99a2f32c3f76d7fec475c9b299f7208b4db29cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650921"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Mettre en forme avec Azure Time Series Insights (préversion)

Cet article définit les meilleures pratiques pour mettre en forme vos charges utiles JSON afin de les ingérer dans Azure Time Series Insights et d’optimiser l'efficacité de vos requêtes en préversion.

## <a name="best-practices"></a>Meilleures pratiques

Il est préférable d'examiner attentivement la façon dont vous envoyez des événements à votre environnement Time Series Insights Preview. 

Les bonnes pratiques générales incluent :

* Envoyer les données aussi efficacement que possible sur le réseau.
* Stocker vos données de manière à en faciliter l’agrégation dans le cadre de votre scénario.

Pour optimiser les performances des requêtes, respectez les règles empiriques suivantes :

* N’envoyez pas de propriétés inutiles. Time Series Insights Preview est facturé en fonction de l’utilisation. Il est préférable de stocker et traiter uniquement les données sur lesquelles vous allez effectuer une requête.
* Utilisez des champs d’instance pour les données statiques. Cette pratique vous évite d’envoyer des données statiques sur le réseau. Les champs d’instance, qui font partie du modèle de série chronologique, fonctionnent comme des données de référence dans le service Time Series Insights qui est généralement disponible. Pour plus d’informations sur les champs d’instance, consultez [Modèle Time Series](./time-series-insights-update-tsm.md).
* Partagez des propriétés de dimension entre deux ou plusieurs événements. Cette pratique vous permet d’envoyer plus efficacement des données sur le réseau.
* N’utilisez pas d’imbrication de tableau approfondie. Time Series Insights (préversion) prend en charge jusqu’à deux niveaux de tableaux imbriqués contenant des objets. Time Series Insights (préversion) aplatit les tableaux dans les messages, en plusieurs événements avec des paires de valeurs de propriétés.
* Si seules quelques mesures existent pour tous ou la plupart des événements, il est préférable d’envoyer ces mesures en tant que propriétés distinctes dans le même objet. Le fait de les envoyer séparément réduit le nombre d’événements et peut accroître les performances des requêtes car moins d’événements doivent être traités.

## <a name="column-flattening"></a>Aplatissement de colonnes

Pendant l’ingestion des données, les charges utiles qui contiennent des objets imbriqués seront aplaties, de telle sorte que le nom de la colonne est une valeur unique avec un délimiteur.

* Par exemple, l’objet JSON imbriqué suivant :

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Devient : `data_flow` si aplati.

> [!IMPORTANT]
> * Azure Time Series Insights Preview utilise des traits de soulignement (`_`) pour la délimitation des colonnes.
> * Notez la différence avec la disponibilité générale, qui utilise plutôt des points (`.`).

Des scénarios plus complexes sont présentés ci-dessous.

#### <a name="example-1"></a>Exemple 1 :

Le scénario suivant comporte deux (ou plus) appareils qui envoient les mesures (signaux) : *débit*, *pression de l’huile moteur*, *température* et *humidité*.

Un seul message IoT Hub est envoyé, dans lequel le tableau externe contient une section partagée de valeurs de dimension courantes (notez les deux entrées d'appareil contenues dans le message).

```JSON
[
  {
    "deviceId":"FXXX",
    "timestamp":"2018-01-17T01:17:00Z",
    "series":[
      {
        "Flow Rate ft3/s":1.0172575712203979,
        "Engine Oil Pressure psi ":34.7
      },
      {
        "Flow Rate ft3/s":2.445906400680542,
        "Engine Oil Pressure psi ":49.2
      }
    ]
  },
  {
    "deviceId":"FYYY",
    "timestamp":"2018-01-17T01:18:00Z",
    "series":[
      {
        "Flow Rate ft3/s":0.58015072345733643,
        "Engine Oil Pressure psi ":22.2
      }
    ]
  }
]
```

**Éléments importants à retenir :**

* L’exemple d’objet JSON contient un tableau externe qui utilise des données d’[instance Time Series](./time-series-insights-update-tsm.md#time-series-model-instances) pour accroître les performances du message. Même si les métadonnées d’appareil des instances Time Series ne sont pas susceptibles de changer, elles offrent souvent des propriétés utiles pour l'analyse des données.

* L’objet JSON combine deux ou plusieurs messages (un provenant de chaque appareil) en une même charge utile, ce qui permet d'économiser de la bande passante dans le temps.

* Les points de données individuels de la série pour chaque appareil sont combinés en un même attribut de **série**, ce qui réduit la nécessité de diffuser en continu les mises à jour pour chaque appareil.

> [!TIP]
> Pour réduire le nombre de messages nécessaires à l'envoi de données et optimiser la télémétrie, vous pouvez regrouper les valeurs de dimensions courantes et les métadonnées de l'instance Time Series dans une même charge utile JSON.

#### <a name="time-series-instance"></a>Instance Time Series 

Examinons de plus près l’utilisation de l'[instance Time Series](./time-series-insights-update-tsm.md#time-series-model-instances) pour optimiser la mise en forme de votre objet JSON. 

> [!NOTE]
> Les [ID Time Series](./time-series-insights-update-how-to-id.md) ci-dessous représentent des *ID d’appareil (deviceId)* .

```JSON
[
  {
    "timeSeriesId":[
      "FXXX"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"REVOLT SIMULATOR",
      "L2":"Battery System"
    }
  },
  {
    "timeSeriesId":[
      "FYYY"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"COMMON SIMULATOR",
      "L2":"Battery System"
    }
  }
]
```

Time Series Insights (préversion) joint une table (après mise à plat) au moment de la requête. La table inclut des colonnes supplémentaires, telles que **Type**.

| deviceId  | Type | L1 | L2 | timestamp | series_Flow Rate ft3/s | series_Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Battery System | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   Battery System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    Battery System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  Le tableau précédent représente la vue de la requête dans l'[Explorateur de préversion Time Series](./time-series-insights-update-explorer.md).

**Éléments importants à retenir :**

* Dans l’exemple précédent, les propriétés statiques sont stockées dans Time Series Insights (préversion) pour optimiser les données envoyées sur le réseau.
* Les données Time Series Insights (préversion) sont jointes au moment de la requête par le biais de l’ID Time Series défini dans l’instance.
* Deux couches d’imbrication sont utilisées. Ce nombre est le plus pris en charge par Time Series Insights (préversion). Il est essentiel d’éviter les tableaux profondément imbriqués.
* Étant donné qu’elles sont peu nombreuses, les mesures sont envoyées en tant que propriétés distinctes dans le même objet. Dans l’exemple, **series_Flow Rate psi**, **series_Engine Oil Pressure psi** et **series_Flow Rate ft3/s** sont des colonnes uniques.

>[!IMPORTANT]
> Les champs d’instance ne sont pas stockés avec les données de télémétrie. Ils sont stockés avec les métadonnées dans le modèle de série chronologique.

#### <a name="example-2"></a>Exemple 2 :

Examinons le code JSON suivant :

```JSON
{
  "deviceId": "FXXX",
  "timestamp": "2019-01-18T01:17:00Z",
  "data": {
        "flow": 1.0172575712203979,
    },
  "data_flow" : 1.76435072345733643
}
```

Dans l’exemple ci-dessus, la propriété `data["flow"]` aplatie présenterait une collision de noms avec la propriété `data_flow`.

Dans ce cas, la valeur de propriété *la plus récente* remplacerait la plus ancienne. 

> [!TIP]
> Contactez l'équipe Time Series Insights pour obtenir de l’aide supplémentaire.

> [!WARNING] 
> * Dans les cas où des propriétés dupliquées sont présentes dans la même (singulière) charge utile d’événement en raison d’une mise à plat ou d’un autre mécanisme, la valeur de propriété la plus récente est stockée, remplaçant toutes les valeurs antérieures.
> * Les séries d’événements combinés ne se substituent pas les unes aux autres.

## <a name="next-steps"></a>Étapes suivantes

* Pour mettre ces instructions en pratique, consultez [Syntaxe des requêtes Azure Time Series Insights Preview](./time-series-insights-query-data-csharp.md). Vous en apprendrez davantage sur la syntaxe de requête pour l’[API REST Time Series Insights (préversion)](https://docs.microsoft.com/rest/api/time-series-insights/preview) pour l’accès aux données.

* Combinez les meilleures pratiques JSON avec le [modèle Time Series](./time-series-insights-update-how-to-tsm.md).
