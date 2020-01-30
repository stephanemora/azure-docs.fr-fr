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
ms.date: 12/16/2019
ms.custom: seodec18
ms.openlocfilehash: fd744e6283b00b0dfdd50805cb628f5bc40ab8d6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846140"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Mettre en forme avec Azure Time Series Insights (préversion)

Cet article vous permet de mettre en forme votre fichier JSON en vue de l’ingérer et d’optimiser l’efficacité de vos requêtes Azure Time Series Insights (préversion).

## <a name="best-practices"></a>Meilleures pratiques

Réfléchissez à la façon dont vous envoyez les événements à Time Series Insights (préversion). Vous devez toujours :

* Envoyer les données aussi efficacement que possible sur le réseau.
* Stocker vos données de manière à en faciliter l’agrégation dans le cadre de votre scénario.

Pour optimiser le niveau de performance des requêtes, procédez comme suit :

* N’envoyez pas de propriétés inutiles. Time Series Insights (préversion) vous est facturé en fonction de votre utilisation. Il est préférable de stocker et traiter les données sur lesquelles vous allez effectuer une requête.
* Utilisez des champs d’instance pour les données statiques. Cette pratique vous évite d’envoyer des données statiques sur le réseau. Les champs d’instance, qui font partie du modèle de série chronologique, fonctionnent comme des données de référence dans le service Time Series Insights qui est généralement disponible. Pour plus d’informations sur les champs d’instance, consultez [Modèle Time Series](./time-series-insights-update-tsm.md).
* Partagez des propriétés de dimension entre deux ou plusieurs événements. Cette pratique vous permet d’envoyer plus efficacement des données sur le réseau.
* N’utilisez pas d’imbrication de tableau approfondie. Time Series Insights (préversion) prend en charge jusqu’à deux niveaux de tableaux imbriqués contenant des objets. Time Series Insights (préversion) aplatit les tableaux dans les messages, en plusieurs événements avec des paires de valeurs de propriétés.
* Si seules quelques mesures existent pour tous ou la plupart des événements, il est préférable d’envoyer ces mesures en tant que propriétés distinctes dans le même objet. Le fait de les envoyer séparément réduit le nombre d’événements et peut accroître les performances des requêtes car moins d’événements doivent être traités.

Pendant l’ingestion des données, les charges utiles qui contiennent l’imbrication sont aplaties, de telle sorte que le nom de la colonne est une valeur unique avec un délimiteur. Time Series Insights (préversion) utilise des traits de soulignement pour la délimitation. Notez qu’il s’agit d’une modification par rapport à la version du produit mise à la disposition générale qui utilisait des points. Pendant la préversion, il existe une mise en garde concernant la mise à plat, illustrée dans le deuxième exemple ci-dessous.

## <a name="examples"></a>Exemples

L’exemple suivant est basé sur un scénario où au moins deux appareils envoient des mesures ou des signaux. Les mesures ou les signaux peuvent concerner le *débit* (Flow Rate), la *pression de l’huile moteur* (Engine Oil Pressure), la *température* et *l’humidité*.

Dans l’exemple, il y a un seul message IoT Hub dans lequel le tableau externe contient une section partagée de valeurs de dimension courantes. Le tableau externe utilise des données d’instance Time Series pour accroître les performances du message. 

L’instance de série chronologique contient les métadonnées de l’appareil. Ces métadonnées ne changent pas avec chaque événement, mais fournissent des propriétés utiles pour l’analyse des données. Pour économiser sur les octets envoyés sur le réseau et rendre le message plus efficace, pensez à utiliser un traitement par lot pour les valeurs de dimension courantes et à utiliser les métadonnées de l’instance de série chronologique.

### <a name="example-1"></a>Exemple 1 :

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

### <a name="time-series-instance"></a>Instance Time Series 

> [!NOTE]
> L’ID Time Series est *deviceId*.

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

Time Series Insights (préversion) joint une table (après mise à plat) au moment de la requête. La table inclut des colonnes supplémentaires, telles que **Type**. L’exemple suivant montre comment [mettre en forme](./time-series-insights-send-events.md#supported-json-shapes) vos données de télémétrie.

| deviceId  | Type | L1 | L2 | timestamp | series_Flow Rate ft3/s | series_Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Battery System | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   Battery System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    Battery System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

Dans l’exemple précédent, notez les points suivants :

* Les propriétés statiques sont stockées dans Time Series Insights (préversion) pour optimiser les données envoyées sur le réseau.
* Les données Time Series Insights (préversion) sont jointes au moment de la requête par le biais de l’ID Time Series défini dans l’instance.
* Deux couches d’imbrication sont utilisées. Ce nombre est le plus pris en charge par Time Series Insights (préversion). Il est essentiel d’éviter les tableaux profondément imbriqués.
* Étant donné qu’elles sont peu nombreuses, les mesures sont envoyées en tant que propriétés distinctes dans le même objet. Dans l’exemple, **series_Flow Rate psi**, **series_Engine Oil Pressure psi** et **series_Flow Rate ft3/s** sont des colonnes uniques.

>[!IMPORTANT]
> Les champs d’instance ne sont pas stockés avec les données de télémétrie. Ils sont stockés avec les métadonnées dans le modèle de série chronologique.
> Le tableau précédent représente la vue de la requête.

### <a name="example-2"></a>Exemple 2 :

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
Dans l’exemple ci-dessus, la propriété `data_flow` aplatie présenterait une collision de noms avec la propriété `data_flow`. Dans ce cas, la valeur de propriété *la plus récente* remplacerait la plus ancienne. Si ce comportement présente un défi pour vos scénarios d’entreprise, veuillez contacter l’équipe TSI.

> [!WARNING] 
> Dans les cas où des propriétés dupliquées sont présentes dans la même charge utile d’événement en raison d’une mise à plat ou d’un autre mécanisme, la valeur de propriété la plus récente est stockée, remplaçant toutes les valeurs antérieures.


## <a name="next-steps"></a>Étapes suivantes

Pour mettre ces instructions en pratique, consultez [Syntaxe des requêtes Azure Time Series Insights Preview](./time-series-insights-query-data-csharp.md). Vous en apprendrez davantage sur la syntaxe de requête pour l’API REST Time Series Insights (préversion) pour l’accès aux données.
