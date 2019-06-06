---
title: Détection d’anomalies dans Azure Stream Analytics
description: Cet article décrit comment utiliser Azure Stream Analytics et Azure Machine Learning pour détecter les anomalies.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: fdf98a0c0c40010bb55955b54dc7b04db8e199f5
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493257"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Détection d’anomalies dans Azure Stream Analytics

Disponible à la fois dans le cloud et Azure IoT Edge, Azure Stream Analytics offre des fonctionnalités de détection des anomalies basées sur un Machine Learning intégré qui peuvent permettre de surveiller les deux anomalies qui se produisent le plus souvent : temporaires et persistantes. Les fonctions **AnomalyDetection_SpikeAndDip** et **AnomalyDetection_ChangePoint** permettent de détecter des anomalies directement dans votre travail Stream Analytics.

Les modèles Machine Learning supposent une série chronologique équitablement échantillonnée. Si la série chronologique n’est pas uniforme, vous pouvez insérer une étape d’agrégation avec une fenêtre bascule avant l’appel de la détection des anomalies.

Les opérations Machine Learning ne prennent pas en charge les tendances de saisonnalité ni les corrélations multivariées.

## <a name="model-accuracy-and-performance"></a>Performances et précision de modèle

En règle générale, la précision du modèle s’améliore avec l’augmentation des données dans la fenêtre glissante. Les données de la fenêtre glissante spécifiée sont traitées comme si elles faisaient partie de la plage normale de valeurs pour cette période. Le modèle ne prend en considération que l’historique des événements de la fenêtre glissante pour vérifier si l’événement actuel est anormal. À mesure que la fenêtre glissante se déplace, les anciennes valeurs sont supprimées de la formation du modèle.

Les fonctions établissent une certaine normale selon ce qu’elles ont déjà vu jusqu’à présent. Les valeurs hors norme sont identifiées par la comparaison par rapport à la normale établie au sein du niveau de confiance. La taille de la fenêtre doit être basée sur le nombre minimal d’événements requis pour former le modèle à un comportement normal afin qu’il puisse reconnaître une anomalie lorsqu’elle se produit.

N’oubliez pas que le temps de réponse du modèle augmente avec la taille de l’historique, car il doit effectuer une comparaison avec un nombre plus élevé d’événements passés. Il est recommandé de n’inclure que le nombre d’événements nécessaires pour optimiser les performances.

Des lacunes dans la série chronologique peuvent être le résultat du modèle qui ne reçoit pas d’événements à certains moments au fil du temps. Cette situation est gérée par Stream Analytics à l’aide de l’imputation. La taille de l’historique, ainsi que la durée, pour la même fenêtre glissante sont utilisées pour calculer la vitesse moyenne à laquelle les événements sont attendus.

## <a name="spike-and-dip"></a>Pics et baisses

Les anomalies temporaires d’un flux d’événements d’une série chronologique sont appelées « pics et baisses ». Les pics et baisses peuvent être surveillés à l’aide de l’opérateur basé sur Machine Learning [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Exemple d’anomalie de pic et de baisse](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Dans la même fenêtre glissante, si un deuxième pic est plus petit que le premier, le score calculé pour le plus petit pic n’est probablement pas suffisamment important par rapport au score du premier pic au niveau de confiance spécifié. Vous pouvez essayer de réduire le paramètre de niveau de confiance du modèle pour détecter ces anomalies. Toutefois, si vous commencez à recevoir trop d’alertes, vous pouvez utiliser un intervalle de confiance plus élevé.

L’exemple de requête suivant suppose une vitesse d’entrée uniforme d’un événement par seconde dans une fenêtre glissante de 2 minutes avec un historique de 120 événements. La dernière instruction SELECT extrait et génère le score et le statut d’anomalie avec un niveau de confiance de 95 %.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Point de changement

Les anomalies persistantes d’un flux d’événements d’une série chronologique sont des modifications de la distribution des valeurs du flux d’événements, telles que des modifications du niveau et des tendances. Dans Stream Analytics, ces anomalies sont détectées à l’aide de l’opérateur basé sur Machine Learning [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics).

Les modifications persistantes durent beaucoup plus longtemps que les pics et les baisses, et peuvent indiquer un ou des événements catastrophiques. Les modifications persistantes ne sont généralement pas visibles à l’œil nu, mais peuvent être détectées avec l’opérateur **AnomalyDetection_ChangePoint**.

L’image suivante est un exemple de modification de niveau :

![Exemple d’anomalie de modification de niveau](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

L’image suivante est un exemple de modification de tendance :

![Exemple d’anomalie de modification de tendance](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

L’exemple de requête suivant suppose une vitesse d’entrée uniforme d’un événement par seconde dans une fenêtre glissante de 20 minutes avec une taille d’historique de 1 200 événements. La dernière instruction SELECT extrait et génère le score et le statut d’anomalie avec un niveau de confiance de 80 %.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Détection d’anomalie avec machine learning dans Azure Stream Analytique

La vidéo suivante montre comment détecter une anomalie en temps réel à l’aide des fonctions machine learning dans Azure Stream Analytique. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

