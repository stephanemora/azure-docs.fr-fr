---
title: JSON Azure Time Series Insights - Meilleures pratiques pour la mise en forme de JSON dans des requêtes Azure Time Series Insights | Microsoft Docs
description: Découvrez comment améliorer l’efficacité de vos requêtes Azure Time Series Insights.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/24/2018
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 284bbf435c7940658753e7bbf1daff00a79d57a1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273848"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>Guide pratique pour la mise en forme de JSON afin d’optimiser les performances des requêtes 

Cet article fournit des conseils pour la mise en forme de JSON, en vue d’optimiser l’efficacité de vos requêtes Azure Time Series Insights (TSI).

## <a name="video"></a>Vidéo : 

### <a name="in-this-video-we-cover-best-practices-around-shaping-json-to-meet-your-storage-needsbr"></a>Dans cette vidéo, nous vous présentons les meilleures pratiques en matière de mise en forme de JSON pour répondre à vos besoins de stockage.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Meilleures pratiques

Il est important de réfléchir à la façon dont vous envoyez les événements à Time Series Insights. Vous devez toujours :

1. Envoyer les données aussi efficacement que possible sur le réseau.
2. Vérifier que vos données sont stockées d’une manière qui vous permet d’effectuer des agrégations adaptées à votre scénario.
3. Vérifier que les limites de propriétés maximales de STI ne sont pas atteintes :
   - 600 propriétés (colonnes) pour les environnements S1.
   - 800 propriétés (colonnes) pour les environnements S2.

Les conseils suivants vous permettront d’obtenir les meilleures performances de requêtes :

1. N’utilisez pas de propriétés dynamiques, telles qu’un ID de balise comme nom de propriété, car vous risquez alors d’atteindre la limite maximale de propriétés.
2. N’envoyez pas de propriétés inutiles. Si une propriété de requête n’est pas obligatoire, il est préférable de ne pas l’envoyer et d’éviter les limitations de stockage.
3. Utilisez des [données de référence](time-series-insights-add-reference-data-set.md) pour éviter d’envoyer des données statiques sur le réseau.
4. Partagez des paramètres de dimension parmi plusieurs événements, afin d’envoyer les données sur le réseau de manière plus efficace.
5. N’utilisez pas d’imbrication de tableau approfondie. STI prend en charge jusqu’à deux niveaux de tableaux imbriqués qui contiennent des objets. STI aplatit les tableaux dans les messages, en plusieurs événements avec des paires de valeurs de propriétés.
6. Si seules quelques mesures existent pour tous ou la plupart des événements, il est préférable d’envoyer ces mesures en tant que propriétés distinctes dans le même objet. Le fait de les envoyer séparément réduit le nombre d’événements et peut accroître les performances des requêtes car moins d’événements doivent être traités. Quand il y a plusieurs mesures, leur envoi en tant que valeurs dans une seule propriété réduit le risque d’atteindre la limite maximale du nombre de propriétés.

## <a name="examples"></a>Exemples

Les deux exemples suivants illustrent l’envoi d’événements, afin de mettre en évidence les recommandations précédentes. Après chaque exemple, vous pouvez observer comment les recommandations ont été appliquées.

Ces exemples sont basés sur un scénario où plusieurs appareils envoient des mesures ou des signaux. Les mesures ou les signaux peuvent concerner le débit (Flow Rate), la pression de l’huile moteur (Engine Oil Pressure), la température et l’humidité. Dans le premier exemple, il y a quelques mesures sur tous les appareils. Dans le deuxième exemple, il y a de nombreux appareils, et chacun envoie de nombreuses mesures uniques.

### <a name="scenario-only-a-few-measurementssignals-exist"></a>Scénario : seuls quelques mesures/signaux existent

**Recommandation :** Envoyez chaque mesure en tant que propriété / colonne distincte.

Dans l’exemple suivant, il y a un seul message IoT Hub, où le tableau externe contient une section partagée de valeurs de dimensions communes. Le tableau externe utilise des données de référence pour accroître les performances du message. Les données de référence contiennent des métadonnées d’appareil qui ne changent pas avec chaque événement, mais fournissent des propriétés utiles pour l’analyse des données. Le fait de traiter par lot les valeurs de dimensions communes et d’utiliser des données de référence permet d’économiser les octets envoyés sur le réseau, ce qui rend le message plus efficace.

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
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

Table de données de référence (la propriété de clé est deviceId) :

| deviceId | messageId | deviceLocation |
| --- | --- | --- |
| FXXX | LINE\_DATA | EU |
| FYYY | LINE\_DATA | FR |

Table d’événements Time Series Insights (après l’aplanissement) :

| deviceId | messageId | deviceLocation |  timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
| --- | --- | --- | --- | --- | --- |
| FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
| FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
| FYYY | LINE\_DATA | FR | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Notez ce qui suit dans l’exemple précédent :

- La colonne **deviceId** sert d’en-tête de colonne pour les différents appareils d’un parc. Le fait de tenter de faire de la valeur deviceId son propre nom de propriété aurait limiter le nombre total d’appareils à 595 (environnements S1) ou à 795 (environnements S2), avec les cinq autres colonnes.

- Les propriétés inutiles sont évitées, par exemple les informations de marque, de modèle, et ainsi de suite. Dans la mesure où elles ne seront pas interrogées ultérieurement, leur suppression permet d’accroître l’efficacité du réseau et du stockage.

- Des données de référence sont utilisées pour réduire le nombre d’octets transférés sur le réseau. Deux attributs, **messageId** et **deviceLocation**, sont joints à l’aide de la propriété de clé, **deviceId**. Ces données sont jointes avec les données de télémétrie au moment de l’entrée, et stockées par la suite dans STI en vue de leur interrogation.

- Deux couches d’imbrication sont utilisées, ce qui est la quantité maximale d’imbrication prise en charge par STI. Il est essentiel d’éviter les tableaux profondément imbriqués.

- Les mesures sont envoyées en tant que propriétés distinctes dans le même objet, car il y en a quelques-unes. Ici, **series.Flow Rate psi** et **series.Engine Oil Pressure ft3/s** sont des colonnes uniques.

### <a name="scenario-several-measures-exist"></a>Scénario : il existe plusieurs mesures

**Recommandation :** Envoyez les mesures comme tuples « type », « unit », « value ».

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

Données de référence (les propriétés de clé sont deviceId et series.tagId) :

| deviceId | series.tagId | messageId | deviceLocation | Type | unité |
| --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | LINE\_DATA | EU | Débit | ft3/s |
| FXXX | oilPressure | LINE\_DATA | EU | Pression d’huile moteur | psi |
| FYYY | pumpRate | LINE\_DATA | FR | Débit | ft3/s |
| FYYY | oilPressure | LINE\_DATA | FR | Pression d’huile moteur | psi |

Table d’événements Time Series Insights (après l’aplanissement) :

| deviceId | series.tagId | messageId | deviceLocation | Type | unité |  timestamp | series.value |
| --- | --- | --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | LINE\_DATA | EU | Débit | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 |
| FXXX | oilPressure | LINE\_DATA | EU | Pression d’huile moteur | psi | 2018-01-17T01:17:00Z | 34.7 |
| FXXX | pumpRate | LINE\_DATA | EU | Débit | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 |
| FXXX | oilPressure | LINE\_DATA | EU | Pression d’huile moteur | Psi | 2018-01-17T01:17:00Z | 49.2 |
| FYYY | pumpRate | LINE\_DATA | FR | Débit | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
| FYYY | oilPressure | LINE\_DATA | FR | Pression d’huile moteur | psi | 2018-01-17T01:18:00Z | 22.2 |

Notez ce qui suit dans l’exemple précédent (comme dans le premier exemple) :

- Les colonnes **deviceId** et **series.tagId** servent d’en-têtes de colonne pour les différents appareils et balises dans un parc. L’utilisation de chacune comme son propre attribut aurait limité la requête à un total de 594 (environnements S1) ou 794 (environnements S2) appareils avec les six autres colonnes.

- Les propriétés inutiles ont été évitées, pour la raison indiquée dans le premier exemple.

- Des données de référence sont utilisées afin de réduire le nombre d’octets transférés sur le réseau en introduisant **deviceId**, pour une paire unique de **messageId** et **deviceLocation**. Une clé composite, **series.tagId**, est utilisée pour la paire unique de **type** et **unit**. La clé composite permet d’utiliser la paire **deviceId** et **series.tagId** pour faire référence à quatre valeurs : **messageId, deviceLocation, type** et **unit**. Ces données sont jointes avec les données de télémétrie au moment de l’entrée, et stockées par la suite dans STI en vue de leur interrogation.

- Deux couches d’imbrication sont utilisées, pour la raison indiquée dans le premier exemple.

### <a name="for-both-scenarios"></a>Pour les deux scénarios

Si vous avez une propriété avec un grand nombre de valeurs possibles, il est préférable de les envoyer en tant que valeurs distinctes dans une seule colonne, plutôt que de créer une colonne pour chaque valeur. Si l’on compare les deux exemples précédents :
  - Dans le premier exemple, peu de propriétés ont plusieurs valeurs. Il est donc plus judicieux de faire de chacune d’elle une propriété distincte. 
  - En revanche, dans le deuxième exemple vous pouvez observer que les mesures ne sont pas spécifiées en tant que propriétés individuelles, mais plutôt en tant que tableau de valeurs/mesures sous une propriété de série commune. Une nouvelle clé (**tagId**) est envoyée, ce qui crée une colonne (**series.tagId**) dans la table aplatie. De nouvelles propriétés (**type** et **unit**) sont créées, à l’aide des données de référence, ce qui évite d’atteindre la limite du nombre de propriétés.

## <a name="next-steps"></a>Étapes suivantes

Pour mettre ces recommandations en pratique, consultez [Syntaxe de requête Azure Time Series Insights](/rest/api/time-series-insights/time-series-insights-reference-query-syntax) afin d’en savoir plus sur la syntaxe de requête pour l’API REST d’accès aux données STI.