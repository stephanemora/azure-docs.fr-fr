---
title: Mettre des événements en forme avec Azure Time Series Insights (préversion) | Microsoft Docs
description: Découvrez comment mettre en forme des événements avec Azure Time Series Insights (préversion).
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 344f3a8b1bdfb789251479baffeb4df15bbf2842
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718359"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Mettre en forme avec Azure Time Series Insights (préversion)

Cet article vous permet de mettre en forme votre fichier JSON en vue d’optimiser l’efficacité de vos requêtes Azure Time Series Insights (préversion).

## <a name="best-practices"></a>Bonnes pratiques

Il est important de réfléchir à la façon dont vous envoyez les événements à Time Series Insights (préversion). Vous devez toujours :

* Envoyer les données aussi efficacement que possible sur le réseau.
* Stocker vos données de manière à en faciliter l’agrégation dans le cadre de votre scénario.

Pour obtenir les meilleures performances de requêtes, procédez comme suit :

* N’envoyez pas de propriétés inutiles. Time Series Insights (préversion) vous est facturé en fonction de votre utilisation. Il est préférable de stocker et traiter les données sur lesquelles vous allez effectuer une requête.
* Utilisez des champs d’instance pour les données statiques. Cette pratique vous évite d’envoyer des données statiques sur le réseau. Les champs d’instance, qui font partie du modèle de série chronologique, fonctionnent comme des données de référence dans le service GA Time Series Insights. Pour en savoir plus sur les champs d’instance, consultez [Time Series Models](./time-series-insights-update-tsm.md) (Modèles de séries chronologiques).
* Partagez des propriétés de dimension entre deux ou plusieurs événements. Cette pratique vous permet d’envoyer plus efficacement des données sur le réseau.
* N’utilisez pas d’imbrication de tableau approfondie. Time Series Insights (préversion) prend en charge jusqu’à deux niveaux de tableaux imbriqués contenant des objets. Time Series Insights (préversion) aplatit les tableaux dans les messages, en plusieurs événements avec des paires de valeurs de propriétés.
* Si seules quelques mesures existent pour tous ou la plupart des événements, il est préférable d’envoyer ces mesures en tant que propriétés distinctes dans le même objet. Le fait de les envoyer séparément réduit le nombre d’événements et peut accroître les performances des requêtes dans la mesure où il y a moins d’événements à traiter.

## <a name="example"></a>Exemples

L’exemple suivant est basé sur un scénario où au moins deux appareils envoient des mesures ou des signaux. Les mesures ou les signaux peuvent concerner le *débit* (Flow Rate), la *pression de l’huile moteur* (Engine Oil Pressure), la *température* et *l’humidité*.

Dans l’exemple suivant, il y a un seul message IoT Hub, où le tableau externe contient une section partagée de valeurs de dimensions communes. Le tableau externe utilise des données d’instance Time Series pour accroître les performances du message. L’instance Time Series contient des métadonnées d’appareil qui ne changent pas avec chaque événement, mais fournissent des propriétés utiles pour l’analyse des données. Pour économiser sur les octets envoyés sur le réseau et rendre le message plus efficace, pensez à utiliser un traitement par lot pour les valeurs dimensionnelles courantes et à utiliser les métadonnées de l’instance Time Series.

### <a name="example-json-payload"></a>Exemple de charge utile JSON

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

### <a name="time-series-instance"></a>Instance Time Series 
> [!NOTE]
> L’ID Time Series est *deviceId*.

```JSON
{
    "timeSeriesId": [
      "FXXX"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "REVOLT SIMULATOR",
      "L2": "Battery System",
    }
  },
  {
    "timeSeriesId": [
      "FYYY"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "COMMON SIMULATOR",
      "L2": "Battery System",
    }
  },
```

Time Series Insights (préversion) joint une table (après mise à plat) au moment de la requête. La table inclut des colonnes supplémentaires, telles que **Type**. L’exemple suivant montre comment [mettre en forme](./time-series-insights-send-events.md#json) vos données de télémétrie :

| deviceId  | Type | L1 | L2 | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | REVOLT SIMULATOR | Battery System | 2018-01-17T01:17:00Z |    1.0172575712203979 |    34.7 |
| `FXXX` | LINE_DATA    REVOLT | SIMULATOR |    Battery System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    Battery System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

Dans l’exemple précédent, notez les points suivants :

* Les propriétés statiques sont stockées dans Time Series Insights (préversion) pour optimiser les données envoyées sur le réseau.
* Les données Time Series Insights (préversion) sont jointes au moment de la requête à l’aide de l’attribut *timeSeriesId* défini dans l’instance.
* Deux couches d’imbrication sont utilisées, ce qui représente le maximum pris en charge par Time Series Insights (préversion). Il est essentiel d’éviter les tableaux profondément imbriqués.
* Étant donné qu’elles sont peu nombreuses, les mesures sont envoyées en tant que propriétés distinctes dans le même objet. Dans l’exemple, **series.Flow Rate psi**, **series.Engine Oil Pressure psi** et **series.Flow Rate ft3/s** sont des colonnes uniques.

>[!IMPORTANT]
> Les champs d’instance ne sont pas stockés avec les données de télémétrie. Ils sont stockés avec les métadonnées dans le **modèle de séries chronologiques**.
> Le tableau précédent représente la vue de la requête.

## <a name="next-steps"></a>Étapes suivantes

- Pour mettre ces instructions en pratique, consultez [Interroger des données à partir de l’environnement Azure Time Series Insights en utilisant C#](./time-series-insights-query-data-csharp.md). Vous en apprendrez davantage sur la syntaxe de requête pour l’API d’accès aux données Time Series Insights (préversion).

- Pour en savoir plus sur les structures JSON prises en charge, consultez [Structures JSON prises en charge](./time-series-insights-send-events.md#json).