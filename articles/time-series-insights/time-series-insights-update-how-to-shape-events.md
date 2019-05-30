---
title: Mettre des événements en forme avec Azure Time Series Insights (préversion) | Microsoft Docs
description: Comprendre comment mettre en forme des événements avec Azure temps série Insights Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: f0e1a79073596dcabfacb7163e12b33bb582b7c3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238926"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Mettre en forme avec Azure Time Series Insights (préversion)

Cet article vous permet de mettre en forme votre fichier JSON en vue d’optimiser l’efficacité de vos requêtes Azure Time Series Insights (préversion).

## <a name="best-practices"></a>Bonnes pratiques

Pensez à la façon dont vous envoyez des événements à temps série Insights Preview. Vous devez toujours :

* Envoyer les données aussi efficacement que possible sur le réseau.
* Stocker vos données de manière à en faciliter l’agrégation dans le cadre de votre scénario.

Pour obtenir les meilleures performances de requêtes, procédez comme suit :

* N’envoyez pas de propriétés inutiles. Time Series Insights (préversion) vous est facturé en fonction de votre utilisation. Il est préférable de stocker et traiter les données sur lesquelles vous allez effectuer une requête.
* Utilisez des champs d’instance pour les données statiques. Cette pratique vous évite d’envoyer des données statiques sur le réseau. Champs d’instance, un composant de modèle de série chronologique, fonctionnent comme référencer des données dans Time Series Insights généralement service disponible. Pour en savoir plus sur les champs d’instance, consultez [Time Series Models](./time-series-insights-update-tsm.md) (Modèles de séries chronologiques).
* Partagez des propriétés de dimension entre deux ou plusieurs événements. Cette pratique vous permet d’envoyer plus efficacement des données sur le réseau.
* N’utilisez pas d’imbrication de tableau approfondie. Temps série Insights Preview prend en charge jusqu'à deux niveaux de tableaux imbriqués qui contiennent des objets. Time Series Insights (préversion) aplatit les tableaux dans les messages, en plusieurs événements avec des paires de valeurs de propriétés.
* Si seules quelques mesures existent pour tous ou la plupart des événements, il est préférable d’envoyer ces mesures en tant que propriétés distinctes dans le même objet. Les envoyer séparément réduit le nombre d’événements et peuvent améliorer les performances des requêtes étant donné que moins d’événements doivent être traités.

## <a name="example"></a>Exemples

L’exemple suivant est basé sur un scénario où au moins deux appareils envoient des mesures ou des signaux. Les mesures ou les signaux peuvent être *taux flux*, *pression d’huile moteur*, *température*, et *humidité*.

Dans l’exemple suivant, il est un seul message Azure IoT Hub, où le tableau externe contient une section partagée commun de valeurs de dimension. Le tableau externe utilise des données d’instance Time Series pour accroître les performances du message. L’instance Time Series contient des métadonnées d’appareil qui ne changent pas avec chaque événement, mais fournissent des propriétés utiles pour l’analyse des données. Pour économiser sur les octets envoyés sur le réseau et rendre le message plus efficace, pensez à utiliser un traitement par lot pour les valeurs dimensionnelles courantes et à utiliser les métadonnées de l’instance Time Series.

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
                "Flow Rate ft3/s": 0.58015072345733643,
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

Time Series Insights (préversion) joint une table (après mise à plat) au moment de la requête. La table inclut des colonnes supplémentaires, telles que **Type**. L’exemple suivant montre comment vous pouvez [forme](./time-series-insights-send-events.md#json) vos données de télémétrie.

| deviceId  | type | L1 | L2 | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Battery System | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   Battery System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    Battery System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

Dans l’exemple précédent, notez les points suivants :

* Les propriétés statiques sont stockées dans Time Series Insights (préversion) pour optimiser les données envoyées sur le réseau.
* Temps série Insights aperçu des données sont jointe au moment de la requête à l’aide de l’ID de série de temps est défini dans l’instance.
* Deux couches d’imbrication sont utilisées, ce qui représente le maximum pris en charge par Time Series Insights (préversion). Il est essentiel d’éviter les tableaux profondément imbriqués.
* Étant donné qu’elles sont peu nombreuses, les mesures sont envoyées en tant que propriétés distinctes dans le même objet. Dans l’exemple, **series.Flow Rate psi**, **series.Engine Oil Pressure psi** et **series.Flow Rate ft3/s** sont des colonnes uniques.

>[!IMPORTANT]
> Champs d’instance ne sont pas stockés avec les données de télémétrie. Ils sont stockés avec des métadonnées dans le **modèle de série chronologique**.
> Le tableau précédent représente la vue de la requête.

## <a name="next-steps"></a>Étapes suivantes

- Pour mettre ces instructions en pratique, consultez [Interroger des données à partir de l’environnement Azure Time Series Insights en utilisant C#](./time-series-insights-query-data-csharp.md). Vous en apprendrez davantage sur la syntaxe de requête pour l’API d’accès aux données Time Series Insights (préversion).
- Pour en savoir plus sur les structures JSON prises en charge, consultez [Structures JSON prises en charge](./time-series-insights-send-events.md#json).
