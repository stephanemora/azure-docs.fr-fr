---
title: Comment utiliser l’API de détecteur d’anomalies sur vos données de série chronologique
description: Apprenez à détecter les anomalies dans vos données sous forme de lot, soit sur la diffusion en continu de données.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 63ede8fe90d5c19c2473ffb315bf6096599ffb9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61432342"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Activation Utiliser l’API de détecteur d’anomalies sur vos données de série chronologique  

Le [API de détecteur d’anomalies](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) propose deux méthodes de détection des anomalies. Vous pouvez soit détecter les anomalies en tant que lot tout au long de votre temps de série, ou que vos données sont générées par la détection de l’état des anomalies du dernier point de données. Le modèle de détection retourne les résultats d’anomalies, ainsi que la valeur attendue de chaque point de données et de l’anomalie supérieure et inférieure des limites de détection. Vous pouvez utiliser ces valeurs pour visualiser la plage des valeurs normales et les anomalies dans les données.

## <a name="anomaly-detection-modes"></a>Mode de détection d’anomalie 

L’API de détecteur d’anomalie fournit les modes de détection : traitement par lots et la diffusion en continu.

> [!NOTE]
> L’URL doivent être combinées avec le point de terminaison approprié pour votre abonnement de demande suivant. Par exemple : `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Détection de lot

Pour détecter les anomalies tout au long d’un lot de points de données sur une période donnée, utilisez l’URI de demande suivant avec vos données de série chronologique : 

`/timeseries/entire/detect`. 

En envoyant vos données de série chronologique en même temps, l’API de générer un modèle à l’aide de la série entière et analyser chaque point de données avec lui.  

### <a name="streaming-detection"></a>Détection de diffusion en continu

Pour détecter les anomalies dans les données de streaming en continu, utilisez l’URI de demande suivant avec votre dernier point de données : 

`/timeseries/last/detect'`. 

En envoyant de nouveaux points de données que vous pouvez les créer, vous pouvez surveiller vos données en temps réel. Un modèle est généré avec les points de données que vous envoyez, et l’API déterminera si le dernier point dans la série chronologique est une anomalie.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>L’ajustement des limites de la détection d’anomalie inférieure et supérieure

Par défaut, les limites supérieures et inférieures pour la détection d’anomalies sont calculées à l’aide de `expectedValue`, `upperMargin`, et `lowerMargin`. Si vous avez besoin des limites différentes, nous vous recommandons d’appliquer un `marginScale` à `upperMargin` ou `lowerMargin`. Les limites est calculées comme suit :

|Limites  |Calcul  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Les exemples suivants montrent un résultat de l’API de détecteur d’anomalies à différentes sensibilités.

### <a name="example-with-sensitivity-at-99"></a>Exemple de sensibilité à 99

![Sensibilité par défaut](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Exemple de sensibilité à 95

![Sensibilité 99](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Exemple de sensibilité à 85

![Sensibilité 85](../media/sensitivity_85.png)

## <a name="next-steps"></a>Étapes suivantes

* [Qu’est l’API de détecteur d’anomalie ?](../overview.md)
* [Démarrage rapide : Détecter les anomalies dans vos données de série chronologique à l’aide de l’API REST de détecteur d’anomalie](../quickstarts/detect-data-anomalies-csharp.md)