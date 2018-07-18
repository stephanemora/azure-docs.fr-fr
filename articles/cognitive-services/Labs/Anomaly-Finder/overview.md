---
title: Qu’est-ce que la Recherche d’anomalies ? Microsoft Cognitive Services | Microsoft Docs
description: Utilisez des algorithmes avancés dans la Recherche d’anomalies pour vous aider à identifier les anomalies dans des données de série chronologique et retourner des informations dans Microsoft Cognitive Services.
services: cognitive-services
author: tonyxing
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 04/19/2018
ms.author: tonyxing
ms.openlocfilehash: 1080bb0ad1d901a8b9a5ace4993d4e0d46924a03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369149"
---
# <a name="what-is-anomaly-finder"></a>Qu’est-ce que la Recherche d’anomalies ?

La Recherche d’anomalies vous permet de surveiller les données au fil du temps et de détecter les anomalies avec un apprentissage automatique qui s’adapte à vos données uniques en appliquant automatiquement le modèle statistique correct, indépendamment du secteur, du scénario ou du volume de données. À l’aide d’une série chronologique en tant qu’entrée, l’API de Recherche d’anomalies indique si un point de données est ou non une anomalie et détermine la valeur attendue, ainsi que les limites supérieure et inférieure pour la visualisation. En tant que service d’intelligence artificielle (AI) prédéfini, la Recherche d’anomalies ne nécessite pas de compétences en Machine Learning autres que l’utilisation d’une API RESTful d’apprentissage. Ceci permet un développement simple et polyvalent, puisqu’elle fonctionne avec toutes les données de série chronologique et peut également être intégrée dans des systèmes de données de streaming. La Recherche d’anomalies englobe un large éventail de cas d’utilisation : des outils financiers pour la gestion des fraudes, des vols, des marchés en mutation et des incidents potentiels, ou la surveillance du trafic de périphériques IoT tout en préservant l’anonymat. Cette solution peut également être monétisée dans le cadre d’un service qui aidera des utilisateurs finaux à comprendre des modifications de données, des dépenses, le retour sur investissement ou l’activité des utilisateurs.
Testez l’API de Recherche d’anomalies et ayez une meilleure compréhension de vos données. 

Regardez ce que vous pouvez faire avec cette API :

* Apprendre à prévoir les valeurs attendues en fonction des données historiques de la série chronologique
* Indiquer si un point de données est une anomalie à partir d’un modèle historique
* Générer une bande pour visualiser la fourchette de valeurs « normales »

![Anomaly_Finder](./media/anomaly_detection1.png) 

Figure 1 : Détecter des anomalies dans les recettes

![Anomaly_Finder](./media/anomaly_detection2.png)

Figure 2 : Détecter des modifications de modèle dans les demandes de service

## <a name="requirements"></a>Configuration requise

- Minimum de données pour la série chronologique d’entrée : au moins 13 points de données pour la série chronologique sans périodicité claire, minimum de 4 cycles de points de données pour la série chronologique dont la périodicité est connue. 
- Intégrité des données : les points de données de la série chronologique sont séparés avec le même intervalle et il ne manque aucun point. 

## <a name="identify-anomalies"></a>Identifier les anomalies

L’API de détection d’anomalies indique si des points de données spécifiques sont ou non des anomalies et fournit les informations supplémentaires ci-dessous.
* Period : la périodicité utilisée par l’API pour détecter les points présentant des anomalies.
* WarningText : le message d’avertissement possible
* ExpectedValue : la valeur prédite par le modèle basé sur l’apprentissage
* IsAnomaly : le résultat indiquant si les points de données sont ou non des anomalies
* IsAnomaly_Neg : le résultat indiquant si les points de données sont des anomalies dans une direction négative (chutes)
* IsAnomaly_Pos : le résultat indiquant si les points de données sont des anomalies dans une direction positive (pics)
* UpperMargin : la somme d’ExpectedValue et d’UpperMargin détermine la limite supérieure où le point de données est encore considéré comme normal.
* LowerMargin : (ExpectedValue - LowerMargin) détermine la limite inférieure où le point de données est encore considéré comme normal.

> [!Note]
> UpperMargin et LowerMargin peuvent être utilisées pour générer une bande autour de la série chronologique réelle afin de visualiser la plage des valeurs normales. 

## <a name="adjusting-lower-and-upper-bounds-in-post-processing-on-the-response"></a>Ajustement des limites inférieures et supérieures de post-traitement pour la réponse

L’API Détection d'anomalies indique si un point de données présente ou non une anomalie, et les limites supérieure et inférieure peuvent être calculées à partir d’ExpectedValue et d’UpperMargin/de LowerMargin. Ces valeurs par défaut devraient parfaitement convenir pour la plupart des cas. Cependant, certains scénarios requièrent d’autres limites que les limites par défaut. La méthode recommandée consiste à appliquer un coefficient à UpperMargin ou à LowerMargin pour ajuster les limites dynamiques.

### <a name="examples-with-1152-as-coefficiency"></a>Exemples avec les coefficients 1/1,5/2

![Sensibilité par défaut](./media/sensitivity_1.png)

![Sensibilité 1,5](./media/sensitivity_1.5.png)

![Sensibilité 2](./media/sensitivity_2.png)

Requête avec des exemples de données

[!INCLUDE [Request](./includes/request.md)]

Exemple de réponse JSON

[!INCLUDE [Response](./includes/response.md)]
