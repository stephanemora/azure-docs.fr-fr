---
title: Architecture de la maintenance prédictive pour l’utilisation de l’API Détecteur d’anomalies (multivarié)
titleSuffix: Azure Cognitive Services
description: Architecture de référence pour l’utilisation des API Détecteur d’anomalies (multivarié) pour appliquer la détection des anomalies à vos données de série chronologique pour la maintenance prédictive.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: détection d’anomalie, Machine Learning, algorithmes
ms.openlocfilehash: 4d379cfe01dcbd88531b98a32b45e0b30f6adeef
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107318811"
---
# <a name="predictive-maintenance-solution-with-anomaly-detector-multivariate"></a>Solution de maintenance prédictive avec Détecteur d’anomalies (multivarié)

De nombreux secteurs différents ont besoin de solutions de maintenance prédictives pour réduire les risques et obtenir des insights actionnables via le traitement des données à partir de leur équipement. La maintenance prédictive évalue la condition de l’équipement en effectuant une surveillance en ligne. L’objectif est de procéder à la maintenance avant la dégradation ou la rupture de l’équipement.

La surveillance de l’état d’intégrité de l’équipement peut être difficile, car chaque composant à l’intérieur de l’équipement peut générer des dizaines de signaux, par exemple les vibrations, l’orientation et la rotation.  Cela peut être encore plus complexe lorsque ces signaux ont une relation implicite, et doivent être surveillés et analysés ensemble. Définir des règles différentes pour ces signaux et les mettre en corrélation manuellement peut être coûteux. La fonctionnalité du Détecteur d’anomalies (multivarié) permet ce qui suit :

* Surveiller plusieurs signaux corrélés ensemble, et de comptabiliser les corrélations entre eux dans le modèle.
* Dans chaque anomalie capturée, le rang de contribution des différents signaux peut être utile avec l’explication de l’anomalie et l’analyse de la cause racine de l’incident.
* Le modèle Détecteur d’anomalies (multivarié) est créé de manière non supervisée. Les modèles peuvent être formés spécifiquement pour différents types d’équipements.

Ici, nous fournissons une architecture de référence pour une solution de maintenance prédictive basée sur un Détecteur d’anomalies (multivarié).

## <a name="reference-architecture"></a>Architecture de référence

[ ![Diagramme architectural qui commence avec des données de capteur collectées en périphérie à l’aide d’un équipement industriel et effectue le suivi du pipeline de traitement/analyse jusqu’à la sortie finale d’une alerte d’incident générée après l’exécution du Détecteur d’anomalies.](../media/multivariate-architecture/multivariate-architecture.png) ](../media/multivariate-architecture/multivariate-architecture.png#lightbox)

Dans l’architecture ci-dessus, les événements de diffusion en continu provenant des données de capteur sont stockés dans Azure Data Lake puis traités par un module de transformation des données pour être convertis dans un format de série chronologique. Pendant ce temps, l’événement de diffusion en continu déclenchera la détection en temps réel avec le modèle formé. En général, il y aura un module pour gérer le cycle de vie du modèle multivarié, comme le *service de pont* dans cette architecture.

**Apprentissage du modèle** : avant d’utiliser le Détecteur d’anomalies (multivarié) pour détecter les anomalies d’un composant ou d’un équipement, nous devons effectuer l’apprentissage d’un modèle sur des signaux spécifiques (série chronologique) générés par cette entité. Le *service de pont* récupère les données d’historique et envoie un travail d’apprentissage au détecteur d’anomalies, puis conserve l’ID de modèle dans le stockage *Métadonnées du modèle*.

**Validation du modèle** : le temps d’apprentissage d’un certain modèle peut varier en fonction du volume de données d’apprentissage. Le *service de pont* peut interroger régulièrement l’état du modèle et les informations de diagnostic. La validation de la qualité du modèle peut être nécessaire avant de le mettre en ligne. S’il existe des étiquettes dans le scénario, elles peuvent être utilisées pour vérifier la qualité du modèle. Dans le cas contraire, les informations de diagnostic peuvent être utilisées pour évaluer la qualité du modèle, et vous pouvez également effectuer une détection sur les données d’historique avec le modèle formé et évaluer le résultat pour vérifier la validité du modèle.

**Inférence de modèle** : la détection en ligne est effectuée avec le modèle valide, et l’ID de résultat peut être stocké dans la *table d’inférence*. Le processus d’apprentissage et le processus d’inférence s’effectuent de manière asynchrone. En règle générale, une tâche de détection peut être effectuée en quelques secondes. Les signaux utilisés pour la détection doivent être les mêmes que ceux qui ont été utilisés pour l’apprentissage. Par exemple, si nous utilisons les vibrations, l’orientation et la rotation pour l’apprentissage, dans la détection, les trois signaux doivent être inclus en tant qu’entrée.

**Alerte d’incident** Les résultats de la détection peuvent être interrogés avec des ID de résultat. Chaque résultat contient la gravité de chaque anomalie et le rang de contribution. Le rang de contribution peut être utilisé pour comprendre pourquoi cette anomalie s’est produite, et quel signal a provoqué cet incident. Différents seuils peuvent être définis sur le niveau de gravité pour générer des alertes et des notifications à envoyer aux ingénieurs de terrain pour effectuer des tâches de maintenance.

## <a name="next-steps"></a>Étapes suivantes

- [Démarrages rapides](../quickstarts/client-libraries-multivariate.md).
- [Meilleures pratiques](../concepts/best-practices-multivariate.md) : cet article porte sur les modèles recommandés à utiliser avec les API multivariées.