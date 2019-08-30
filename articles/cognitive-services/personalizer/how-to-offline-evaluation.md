---
title: Évaluation hors connexion — Personalizer
titleSuffix: Azure Cognitive Services
description: Découvrez comment analyser votre boucle d’apprentissage avec une évaluation hors connexion
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a8a75601daf36ca21ea56a5930219d7d467f0c85
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69557826"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>Comment analyser votre boucle d’apprentissage avec une évaluation hors connexion


Découvrez comment effectuer une évaluation hors connexion et comment comprendre les résultats.

Les évaluations hors connexion vous permettent de mesurer l’efficacité de Personalizer en comparaison du comportement par défaut de votre application, de trouver quelles caractéristiques contribuent le plus à la personnalisation, et de découvrir automatiquement de nouveaux paramètres du machine learning.

Pour plus d’informations, consultez [Évaluations hors connexion](concepts-offline-evaluation.md).


## <a name="prerequisites"></a>Prérequis

1. Vous devez avoir une boucle Personalizer configurée.
1. La boucle Personalizer doit avoir une quantité de données représentative : pour fournir un ordre de grandeur, nous recommandons au moins 50 000 événements dans ses journaux pour obtenir des résultats d’évaluation significatifs.

Si vous le souhaitez, vous pouvez aussi avoir des fichiers de _stratégie d’apprentissage_ préalablement exportés que vous pouvez comparer et tester dans la même évaluation.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Étapes pour démarrer une nouvelle évaluation hors connexion

1. Recherchez votre ressource de boucle de personnalisation dans le portail Azure.
1. Accédez à la section « Évaluation ».
1. Cliquez sur Nouvelle évaluation.
1. Sélectionnez une date de début et de fin pour l’évaluation hors connexion. Il s’agit de dates dans le passé, qui spécifient la plage de données à utiliser dans l’évaluation. Ces données doivent être présentes dans les journaux, tel que spécifié dans le paramètre [Conservation des données](how-to-settings.md).
1. Si vous le souhaitez, vous pouvez charger votre propre stratégie d’apprentissage. 
1. Spécifiez si Personalizer doit créer une stratégie d’apprentissage optimisée en fonction du comportement des utilisateurs observé dans cette période.
1. Démarrer l’évaluation

## <a name="results"></a>Résultats

L’exécution des évaluations peut durer longtemps, selon la quantité de données à traiter, le nombre de stratégies d’apprentissage à comparer, et si une optimisation a été demandée.

Une fois qu’elle est terminée, vous pouvez voir les résultats suivants :

1. Comparaisons des stratégies d’apprentissage, notamment :
    * **Stratégie en ligne** : La stratégie d’apprentissage actuelle utilisée dans Personalizer
    * **Base de référence** : Valeur par défaut de l’application (telle que déterminée par la première action envoyée dans les appels du classement),
    * **Stratégie aléatoire** : Un comportement de classement imaginaire qui retourne toujours un choix aléatoire d’actions parmi celles fournies.
    * **Stratégies personnalisées** : Stratégies d’apprentissage supplémentaires chargées lors du démarrage de l’évaluation.
    * **Stratégie optimisée** : Si l’évaluation a été démarrée avec l’option pour découvrir une stratégie optimisée, elle sera également comparée, et vous pourrez la télécharger ou en faire la stratégie d’apprentissage en ligne, remplaçant la stratégie actuelle.

1. Efficacité des [caractéristiques](concepts-features.md) pour les actions et le contexte.


## <a name="more-information"></a>Informations complémentaires

* Découvrez [comment les évaluations hors connexion fonctionnent](concepts-offline-evaluation.md).
