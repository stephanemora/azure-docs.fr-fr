---
title: Guide pratique pour effectuer une évaluation hors connexion - Personalizer
titleSuffix: Azure Cognitive Services
description: Cet article vous montre comment utiliser l’évaluation hors connexion pour mesurer l’efficacité de votre application et analyser votre boucle d’apprentissage.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: c2aec0db2d1f9865188f2749a0eeb765a14d04ed
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73952996"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analysez votre boucle d’apprentissage avec une évaluation hors connexion

Découvrez comment effectuer une évaluation hors connexion et comment comprendre les résultats.

Les évaluations hors connexion vous permettent de mesurer l’efficacité de Personalizer en comparaison du comportement par défaut de votre application, de trouver quelles caractéristiques contribuent le plus à la personnalisation, et de découvrir automatiquement de nouvelles valeurs de l’apprentissage automatique.

Pour plus d’informations, consultez [Évaluations hors connexion](concepts-offline-evaluation.md).


## <a name="prerequisites"></a>Prérequis

* Boucle Personalizer configurée
* La boucle Personalizer doit avoir une quantité de données représentative : pour fournir un ordre de grandeur, nous recommandons au moins 50 000 événements dans ses journaux pour obtenir des résultats d’évaluation significatifs. Si vous le souhaitez, vous pouvez aussi avoir des fichiers de _stratégie d’apprentissage_ préalablement exportés que vous pouvez comparer et tester dans la même évaluation.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Étapes pour démarrer une nouvelle évaluation hors connexion

1. Sur le [portail Azure](https://azure.microsoft.com/free/), recherchez votre ressource Personnalisation.
1. Sur le portail Azure, accédez à la section **Évaluations**, puis sélectionnez l’option **Créer une évaluation**.
    ![Sur le portail Azure, accédez à la section **Évaluations**, puis sélectionnez l’option **Créer une évaluation**.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Configurez les valeurs suivantes :

    * Un nom d’évaluation
    * Dates de début et de fin : il s’agit de dates dans le passé, qui spécifient la plage de données à utiliser pour l’évaluation. Ces données doivent être présentes dans les journaux, tel que spécifié dans la valeur de [Conservation des données](how-to-settings.md).
    * Découverte d’optimisation définie sur **Oui**

    ![Choisissez les paramètres d’évaluation hors connexion](./media/offline-evaluation/create-an-evaluation-form.png)

1. Démarrez l’Évaluation en sélectionnant **OK**. 

## <a name="results"></a>Résultats

L’exécution des évaluations peut durer longtemps, selon la quantité de données à traiter, le nombre de stratégies d’apprentissage à comparer, et si une optimisation a été demandée.

Une fois l’opération terminée, vous pouvez sélectionner l’évaluation dans la liste des évaluations. 

Les comparaisons des stratégies d’apprentissage sont les suivantes :

* **Stratégie en ligne** : La stratégie d’apprentissage actuelle utilisée dans Personalizer
* **Base de référence** : Valeur par défaut de l’application (telle que déterminée par la première action envoyée dans les appels du classement),
* **Stratégie aléatoire** : Un comportement de classement imaginaire qui retourne toujours un choix aléatoire d’actions parmi celles fournies.
* **Stratégies personnalisées** : Stratégies d’apprentissage supplémentaires chargées lors du démarrage de l’évaluation.
* **Stratégie optimisée** : Si l’évaluation a été démarrée avec l’option pour découvrir une stratégie optimisée, elle sera également comparée, et vous pourrez la télécharger ou en faire la stratégie d’apprentissage en ligne, remplaçant la stratégie actuelle.

![Graphique des résultats des paramètres d’évaluation hors connexion](./media/offline-evaluation/evaluation-results.png)

Efficacité des [caractéristiques](concepts-features.md) pour les actions et le contexte.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez [comment les évaluations hors connexion fonctionnent](concepts-offline-evaluation.md).
