---
title: Qu’est l’API de détecteur d’anomalie ? | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Utiliser des algorithmes avancés de l’API de détecteur d’anomalies pour identifier les anomalies dans vos données de série chronologique.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 48dffb8853d3def590e434c1683ecf038e2a194e
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473200"
---
# <a name="what-is-the-anomaly-detector-api"></a>Qu’est l’API de détecteur d’anomalie ?

L’API de détecteur d’anomalie vous permet de surveiller et de détecter les anomalies dans vos données de série chronologique avec machine learning. L’API de détecteur d’anomalie s’adapte en identifiant et en appliquant les modèles mieux adaptés à vos données, quel que soit le secteur d’activité, un scénario ou volume de données automatiquement. À l’aide de vos données de série chronologique, l’API détermine les limites pour la détection des anomalies, les valeurs attendues, et les points de données sont les anomalies.

![Détecter des modifications de modèle dans les demandes de service](./media/anomaly_detection2.png)

À l’aide du détecteur d’anomalies ne nécessite aucune expérience préalable dans machine learning, et l’API RESTful vous permet d’intégrer facilement le service dans vos applications et processus.

## <a name="features"></a>Caractéristiques

Avec le détecteur d’anomalies, vous pouvez automatiquement détecter des anomalies dans l’ensemble de vos données de série chronologique, ou qu’ils se produisent en temps réel. 

|Fonctionnalité  |Description  |
|---------|---------|
|Détecter les anomalies qu’ils se produisent en temps réel. | Détecter les anomalies dans vos données de diffusion en continu à l’aide de points de données affichée précédemment pour déterminer si votre dernière est une anomalie. Cette opération génère un modèle à l’aide de points de données que vous envoyez et détermine si le point cible est une anomalie. En appelant l’API avec chaque nouveau point de données que vous générez, vous pouvez surveiller vos données, tel qu’il est créé. |
|Détecter les anomalies tout au long de votre jeu de données en tant que lot. | Utilisez votre série chronologique pour détecter d’éventuelles anomalies qui peuvent exister dans l’ensemble de vos données. Cette opération génère un modèle à l’aide de vos données de série chronologique entière, avec chaque point de les analyser avec le même modèle.         |
| Obtenir des informations supplémentaires sur vos données. | Obtenir des informations utiles sur vos données et toute anomalie observée, y compris les valeurs attendues, les limites d’anomalies et positions. |
| Ajuster les limites de détection des anomalies. | L’API de détecteur d’anomalie crée automatiquement des limites pour la détection des anomalies. Ajuster ces limites pour augmenter ou diminuer la sensibilité de l’API aux anomalies de données et mieux adaptée à vos données. |

## <a name="demo"></a>Démonstration

Pour commencer rapidement à l’aide de l’API de détecteur d’anomalie, essayez une [démonstration en ligne](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) qui peuvent être exécutés dans votre navigateur. Cette démonstration s’exécute dans un bloc-notes Jupyter hébergé sur le web et vous montre comment envoyer une demande d’API et visualiser le résultat.

Pour exécuter la démonstration, procédez comme suit :

1. Obtenir une clé d’abonnement API de détecteur d’anomalies valide et un point de terminaison d’API. La section ci-dessous comporte des instructions pour s’inscrire. 
2. Connectez-vous, puis cliquez sur Cloner, dans le coin supérieur droit.
3. Cliquez sur **s’exécutent sur le calcul gratuites**
4. Sélectionnez un des ordinateurs portables pour cet exemple.
5. Ajoutez votre clé d’abonnement API de détecteur d’anomalies valide pour le `subscription_key` variable. Modifier le `endpoint` variable à votre point de terminaison. Par exemple : `https://westus2.api.cognitive.microsoft.com`
1. Dans la barre de menus supérieure, cliquez sur **cellule**, puis **exécuter tout**.

## <a name="workflow"></a>Workflow

L’API de détecteur d’anomalie est un service web RESTful, facilitant ainsi l’appeler à partir de n’importe quel langage de programmation qui peut effectuer des requêtes HTTP et analyser JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Après l’inscription :

1. Tenir vos données de série chronologique et le convertir en un format JSON valide. Utilisez [meilleures pratiques](concepts/anomaly-detection-best-practices.md) lors de la préparation de vos données pour obtenir les meilleurs résultats.
1. Envoyer une demande à l’API de détecteur d’anomalie avec vos données.
1. Traitez la réponse de l’API en analysant le message JSON renvoyé.

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Détecter les anomalies dans vos données de série chronologique à l’aide de l’API REST de détecteur d’anomalie](quickstarts/detect-data-anomalies-csharp.md)
* L’API de détecteur d’anomalie [démonstration en ligne](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* Le détecteur d’anomalies [référence d’API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)