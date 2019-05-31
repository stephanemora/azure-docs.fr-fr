---
title: Bonnes pratiques pour la mise en forme de JSON dans les requêtes Azure Time Series Insights | Microsoft Docs
description: Découvrez comment améliorer l’efficacité de vos requêtes Azure Time Series Insights.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 089285637bb740fea47f1fd07de0906dfe46662b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244458"
---
# <a name="shape-json-to-maximize-query-performance"></a>Mettre en forme JSON afin d’optimiser les performances des requêtes 

Cet article fournit des conseils quant à la forme JSON pour optimiser l’efficacité de vos requêtes Azure Time Series Insights.

## <a name="video"></a>Vidéo

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Découvrez les meilleures pratiques pour la mise en forme de JSON pour répondre à vos besoins de stockage.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Bonnes pratiques
Pensez à la façon dont vous envoyez des événements à Time Series Insights. À savoir, vous avez toujours :

1. Envoyer les données aussi efficacement que possible sur le réseau.
1. Assurez-vous que vos données sont stockées de manière afin que vous pouvez effectuer des agrégations appropriées à votre scénario.
1. Assurez-vous que vous n’atteignez pas les limites de la propriété maximum Time Series Insights de :
   - 600 propriétés (colonnes) pour les environnements S1.
   - 800 propriétés (colonnes) pour les environnements S2.

Le guide suivant permet d’assurer les meilleurs résultats possibles :

1. N’utilisez pas les propriétés dynamiques, par exemple un ID de balise, comme un nom de propriété. Cette utilisation contribue à atteindre la limite maximale de propriétés.
1. N’envoyez pas de propriétés inutiles. Si une propriété de la requête n’est pas obligatoire, il est préférable de ne pas envoyer. Ainsi, vous évitez les limitations de stockage.
1. Utilisez [données de référence](time-series-insights-add-reference-data-set.md) d’éviter l’envoi des données statiques sur le réseau.
1. Partager des propriétés de dimension entre plusieurs événements à envoyer des données sur le réseau plus efficacement.
1. N’utilisez pas d’imbrication de tableau approfondie. Time Series Insights prend en charge jusqu'à deux niveaux de tableaux imbriqués qui contiennent des objets. Time Series Insights aplatit les tableaux dans les messages en plusieurs événements avec les paires propriété / valeur.
1. Si seules quelques mesures existent pour tous ou la plupart des événements, il est préférable d’envoyer ces mesures en tant que propriétés distinctes dans le même objet. Les envoyer séparément réduit le nombre d’événements et peuvent améliorer les performances des requêtes étant donné que moins d’événements doivent être traités. Lorsqu’il existe plusieurs mesures, les envoyer en tant que valeurs dans une propriété unique réduit le risque d’atteindre la limite maximale de propriété.

## <a name="example-overview"></a>Vue d’ensemble de l’exemple

Les deux exemples suivants montrent comment envoyer des événements pour mettre en évidence les recommandations précédentes. Chaque exemple, vous pouvez voir comment les recommandations ont été appliquées.

Ces exemples sont basés sur un scénario où plusieurs appareils envoient des mesures ou des signaux. Taux de flux, pression d’huile moteur, température et humidité de mesures ou des signaux peuvent être. Dans le premier exemple, il y a quelques mesures sur tous les appareils. Le deuxième exemple a de nombreux appareils, et chaque périphérique envoie de nombreuses mesures uniques.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scénario n° 1 : Existent uniquement quelques mesures

> [!TIP]
> Nous vous recommandons d’envoyer chaque mesure ou signal comme une propriété distincte ou d’une colonne.

Dans l’exemple suivant, il est un seul message Azure IoT Hub, où le tableau externe contient une section partagée commun de valeurs de dimension. Le tableau externe utilise des données de référence pour accroître les performances du message. Données de référence contient des métadonnées de l’appareil qui ne change pas avec chaque événement, mais il fournit des propriétés utiles pour l’analyse de données. Les valeurs de dimension courantes de traitement par lot et employant référence enregistre les données sur les octets envoyés sur le réseau, ce qui rend le message plus efficace.

Exemple de charge utile JSON :

```json
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

* Table de données de référence qui a la propriété de clé **deviceId**:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | FR |

* Table d’événements de Series Insights d’heure, après la mise à plat :

   | deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | FR | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Remarques sur ces deux tables :

- La colonne **deviceId** sert d’en-tête de colonne pour les différents appareils d’un parc. Rendez la valeur deviceId son propre nom de la propriété limite le nombre total d’appareils à 595 (pour les environnements de S1) ou 795 (pour les environnements de S2) avec les cinq autres colonnes.
- Les propriétés inutiles sont évitées pour exemple, la marque et le modèle d’informations. Étant donné que les propriétés ne sont pas être interrogées à l’avenir, leur suppression permet une meilleure réseau et l’efficacité du stockage.
- Des données de référence sont utilisées pour réduire le nombre d’octets transférés sur le réseau. Les deux attributs **messageId** et **deviceLocation** sont jointes à l’aide de la propriété de clé **deviceId**. Ces données sont jointe au moment de l’entrée avec les données de télémétrie et sont ensuite stockées dans Time Series Insights pour l’interrogation.
- Deux couches d’imbrication sont utilisés, qui est la quantité maximale d’imbrication pris en charge par Time Series Insights. Il est essentiel d’éviter les tableaux profondément imbriqués.
- Les mesures sont envoyés en tant que propriétés distinctes dans le même objet, car il existe quelques mesures. Ici, **series.Flow Rate psi** et **series.Engine Oil Pressure ft3/s** sont des colonnes uniques.

## <a name="scenario-two-several-measures-exist"></a>Scénario n ° 2 : Plusieurs mesures existent

> [!TIP]
> Nous vous recommandons d’envoyer des mesures comme « type », « unité » et « valeur » de tuples.

Exemple de charge utile JSON :

```json
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

* Table de données de référence qui a les propriétés de clé **deviceId** et **series.tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | type | unité |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Débit | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | Pression d’huile moteur | psi |
   | FYYY | pumpRate | LINE\_DATA | FR | Débit | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | FR | Pression d’huile moteur | psi |

* Table d’événements de Series Insights d’heure, après la mise à plat :

   | deviceId | series.tagId | messageId | deviceLocation | type | unité | timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Débit | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Pression d’huile moteur | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | Débit | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Pression d’huile moteur | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | FR | Débit | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | FR | Pression d’huile moteur | psi | 2018-01-17T01:18:00Z | 22.2 |

Remarques sur ces deux tables :

- Les colonnes **deviceId** et **series.tagId** servir les en-têtes de colonnes pour les différents appareils et les balises dans un parc. En utilisant chacune comme son propre attribut limite la requête aux 594 (pour les environnements de S1) ou 794 (pour les environnements de S2) des appareils avec les six colonnes au total.
- les propriétés inutiles ont été évitées, pour la raison indiquée dans le premier exemple.
- Données de référence sont utilisées pour réduire le nombre d’octets transférés sur le réseau en introduisant **deviceId**, qui est utilisé pour la paire unique de **messageId** et **deviceLocation**. La clé composite **series.tagId** est utilisé pour la paire unique de **type** et **unité**. La clé composite permet la **deviceId** et **series.tagId** paire à utiliser pour faire référence à quatre valeurs : **messageId, deviceLocation, tapez,** et **unité**. Ces données sont jointe avec les données de télémétrie au moment de l’entrée. Il est ensuite stocké dans Time Series Insights pour l’interrogation.
- deux couches d’imbrication sont utilisés pour la raison indiquée dans le premier exemple.

### <a name="for-both-scenarios"></a>Pour les deux scénarios

Pour une propriété avec un grand nombre de valeurs possibles, il est préférable à envoyer en tant que valeurs distinctes dans une seule colonne au lieu de créer une nouvelle colonne pour chaque valeur. Si l’on compare les deux exemples précédents :

  - Dans le premier exemple, quelques propriétés ont plusieurs valeurs, il est nécessaire pour apporter chacune une propriété distincte.
  - Dans le deuxième exemple, les mesures ne sont pas spécifiés en tant que propriétés individuelles. Au lieu de cela, il s’agit d’un tableau de valeurs ou les mesures sous une propriété commune de la série. La nouvelle clé **tagId** est envoyé, ce qui crée la nouvelle colonne **series.tagId** dans le tableau aplati. Les nouvelles propriétés **type** et **unité** sont créés à l’aide de données de référence afin que la limite de la propriété n’est pas atteinte.

## <a name="next-steps"></a>Étapes suivantes

- Lecture [syntaxe de requête Azure Time Series Insights](/rest/api/time-series-insights/ga-query-syntax) pour en savoir plus sur la syntaxe de requête pour les données Time Series Insights accéder à l’API REST.
- En savoir plus [comment les événements de la forme](./time-series-insights-send-events.md).
