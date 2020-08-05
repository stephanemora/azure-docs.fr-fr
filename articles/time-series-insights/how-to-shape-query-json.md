---
title: Bonnes pratiques pour la mise en forme de JSON - Requêtes Azure Time Series Insights | Microsoft Docs
description: Découvrez comment améliorer l’efficacité de vos requêtes Azure Time Series Insights en mettant en forme JSON.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.service: time-series-insights
ms.topic: article
ms.date: 06/30/2020
ms.custom: seodec18
ms.openlocfilehash: cc24c1f49a48e81509961d5d7d01dba60dc50475
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077645"
---
# <a name="shape-json-to-maximize-query-performance-in-your-gen1-environment"></a>Mise en forme de JSON afin d’optimiser les performances des requêtes dans votre environnement Gen1

Cet article fournit des conseils pour la procédure de mise en forme de JSON, en vue d’optimiser l’efficacité de vos requêtes Azure Time Series Insights.

## <a name="video"></a>Vidéo

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Découvrez les meilleures pratiques en matière de mise en forme de JSON pour répondre à vos besoins de stockage.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Meilleures pratiques

Réfléchissez à la façon dont vous envoyez les événements à Azure Time Series Insights. À savoir, vous pouvez toujours :

1. Envoyer les données aussi efficacement que possible sur le réseau.
1. Vérifier que vos données sont stockées d’une manière qui vous permet d’effectuer des agrégations adaptées à votre scénario.
1. Vérifier que les limites de propriétés maximales d’Azure Time Series Insights ne sont pas atteintes :
   - 600 propriétés (colonnes) pour les environnements S1.
   - 800 propriétés (colonnes) pour les environnements S2.

Les conseils suivants vous permettront d’obtenir les meilleures performances de requêtes :

1. N’utilisez pas de propriétés dynamiques, telles qu’un ID de balise comme nom de propriété. En effet, vous risqueriez alors d’atteindre la limite maximale de propriétés.
1. N’envoyez pas de propriétés inutiles. Si une propriété de requête n’est pas obligatoire, il est préférable de ne pas l’envoyer. Vous évitez ainsi les limitations de stockage.
1. Utilisez des [données de référence](time-series-insights-add-reference-data-set.md) pour éviter d’envoyer des données statiques sur le réseau.
1. Partagez des paramètres de dimension parmi plusieurs événements, afin d’envoyer les données sur le réseau de manière plus efficace.
1. N’utilisez pas d’imbrication de tableau approfondie. Azure Time Series Insights prend en charge jusqu’à deux niveaux de tableaux imbriqués contenant des objets. Azure Time Series Insights aplanit les tableaux dans les messages en plusieurs événements avec des paires de valeurs de propriétés.
1. Si seules quelques mesures existent pour tous ou la plupart des événements, il est préférable d’envoyer ces mesures en tant que propriétés distinctes dans le même objet. Le fait de les envoyer séparément réduit le nombre d’événements et peut accroître les performances des requêtes car moins d’événements doivent être traités. Quand il y a plusieurs mesures, leur envoi en tant que valeurs dans une seule propriété réduit le risque d’atteindre la limite maximale du nombre de propriétés.

## <a name="example-overview"></a>Présentation des exemples

Les deux exemples suivants illustrent la procédure d’envoi d’événements afin de mettre en évidence les recommandations précédentes. Après chaque exemple, vous pouvez observer comment les recommandations ont été appliquées.

Ces exemples sont basés sur un scénario où plusieurs appareils envoient des mesures ou des signaux. Les mesures ou les signaux peuvent concerner le débit (Flow Rate), la pression de l’huile moteur (Engine Oil Pressure), la température et l’humidité. Dans le premier exemple, il y a quelques mesures sur tous les appareils. Le deuxième exemple comporte de nombreux appareils, dont chacun envoie de nombreuses mesures uniques.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scénario 1 : seules quelques mesures existent

> [!TIP]
> Nous vous recommandons d’envoyer chaque mesure ou signal en tant que propriété ou colonne distincte.

Dans l’exemple suivant, il y a un seul message Azure IoT Hub, où le tableau externe contient une section partagée de valeurs de dimensions communes. Le tableau externe utilise des données de référence pour accroître les performances du message. Les données de référence contiennent des métadonnées d’appareil qui ne changent pas avec chaque événement, mais fournissent des propriétés utiles pour l’analyse des données. Le fait de traiter par lot les valeurs de dimensions communes et d’utiliser des données de référence permet d’économiser les octets envoyés sur le réseau, ce qui rend le message plus efficace.

Considérez la charge utile JSON suivante envoyée à votre environnement Azure Time Series Insights en disponibilité générale à l’aide d’un [objet de message d’appareil IoT](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) qui est sérialisé en JSON lors de son envoi vers le cloud Azure :


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

* Table de données de référence ayant la propriété de clé **deviceId** :

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | US |

* Table d’événements Azure Time Series Insights, après l’aplanissement :

   | deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | US | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]
> - La colonne **deviceId** sert d’en-tête de colonne pour les différents appareils d’un parc. Faire de la valeur **deviceId** son propre nom de propriété limite le nombre total d’appareils à 595 (pour les environnements S1) ou à 795 (pour les environnements S2), avec les cinq autres colonnes.
> - Les propriétés inutiles sont évitées (par exemple les informations de marque et de modèle). Du fait que les propriétés ne seront pas interrogées ultérieurement, leur suppression permet d’accroître l’efficacité du réseau et du stockage.
> - Des données de référence sont utilisées pour réduire le nombre d’octets transférés sur le réseau. Les deux attributs, **messageId** et **deviceLocation**, sont joints à l’aide de la propriété de clé, **deviceId**. Ces données sont jointes avec les données de télémétrie au moment de l’entrée, puis stockées dans Azure Time Series Insights en vue de leur interrogation.
> - Deux couches d’imbrication sont utilisées, ce qui est la quantité maximale d’imbrication prise en charge par Azure Time Series Insights. Il est essentiel d’éviter les tableaux profondément imbriqués.
> - Les mesures sont envoyées en tant que propriétés distinctes dans le même objet, étant donné qu’elles sont peu nombreuses. Ici, **series.Flow Rate psi** et **series.Engine Oil Pressure ft3/s** sont des colonnes uniques.

## <a name="scenario-two-several-measures-exist"></a>Scénario 2 : il existe plusieurs mesures

> [!TIP]
> Nous recommandons d’envoyer les mesures comme tuples « type », « unit » et « value ».

Exemple de charge utile JSON :

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

* Données de la table de référence ayant les propriétés de clé **deviceId** et **series.tagId** :

   | deviceId | series.tagId | messageId | deviceLocation | type | unité |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Débit | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | Pression d’huile moteur | psi |
   | FYYY | pumpRate | LINE\_DATA | US | Débit | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | US | Pression d’huile moteur | psi |

* Table d’événements Azure Time Series Insights, après l’aplanissement :

   | deviceId | series.tagId | messageId | deviceLocation | type | unité | timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Débit | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Pression d’huile moteur | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | Débit | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Pression d’huile moteur | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | US | Débit | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | US | Pression d’huile moteur | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]
> - Les colonnes **deviceId** et **series.tagId** servent d’en-têtes de colonne pour les différents appareils et balises dans un parc. L’utilisation de chacune comme son propre attribut limite la requête à un total de 594 (pour les environnements S1) ou 794 (pour les environnements S2) appareils avec les six autres colonnes.
> - Les propriétés inutiles ont été évitées, pour la raison indiquée dans le premier exemple.
> - Des données de référence sont utilisées afin de réduire le nombre d’octets transférés sur le réseau en introduisant **deviceId**, qui est utilisée pour la paire unique de **messageId** et **deviceLocation**. Une clé composite, **series.tagId**, est utilisée pour la paire unique de **type** et **unit**. La clé composite permet d’utiliser la paire **deviceId** et **series.tagId** pour faire référence à quatre valeurs : **messageId, deviceLocation, type** et **unit**. Ces données sont jointes avec les données de télémétrie au moment de l’entrée. Elles sont ensuite stockées dans Azure Time Series Insights pour être interrogées.
> - Deux couches d’imbrication sont utilisées, pour la raison indiquée dans le premier exemple.

### <a name="for-both-scenarios"></a>Pour les deux scénarios

Pour une propriété avec un grand nombre de valeurs possibles, il est préférable de les envoyer en tant que valeurs distinctes dans une seule colonne au lieu de créer une colonne pour chaque valeur. Si l’on compare les deux exemples précédents :

  - Dans le premier exemple, quelques propriétés ont plusieurs valeurs. Il est donc plus judicieux de faire de chacune d’elle une propriété distincte.
  - Dans le deuxième exemple, les mesures ne sont pas spécifiés en tant que propriétés individuelles. Il s’agit plutôt d’un ensemble de valeurs ou de mesures dans le cadre d’une propriété de série commune. La nouvelle clé **tagId** est envoyée, ce qui crée une colonne **series.tagId** dans la table aplatie. Les nouvelles propriétés **type** et **unité** sont créées à l’aide des données de référence pour que la limite de propriété ne soit pas atteinte.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’envoi des [messages des appareils IoT Hub vers le cloud](../iot-hub/iot-hub-devguide-messages-construct.md).

- Lisez [Syntaxe de requête Azure Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax) afin d’en savoir plus sur la syntaxe de requête pour l’API REST d’accès aux données Azure Time Series Insights.

- Découvrez [comment mettre en forme les événements](./time-series-insights-send-events.md).
