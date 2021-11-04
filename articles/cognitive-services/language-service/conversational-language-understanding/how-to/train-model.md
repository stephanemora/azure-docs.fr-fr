---
title: Comment effectuer l’apprentissage et l’évaluation de modèles dans le Language Understanding de conversation
titleSuffix: Azure Cognitive Services
description: Utilisez cet article pour effectuer l’apprentissage d’un modèle et afficher ses détails d’évaluation pour apporter des améliorations.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: a5f4ccb11b759f735618ef991e7dfd85fd071035
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096642"
---
# <a name="train-and-evaluate-models"></a>Effectuer l'apprentissage et évaluer des modèles

Une fois que vous avez terminé de [baliser vos énoncés](./tag-utterances.md), vous pouvez effectuer l’apprentissage de votre modèle. La formation consiste à convertir l’état actuel des données d’apprentissage de votre projet pour créer un modèle qui peut être utilisé pour les prédictions. À chaque apprentissage, vous devez nommer votre instance de formation. 

Vous pouvez créer et former plusieurs modèles au sein d’un même projet. Toutefois, si vous reformez un modèle spécifique, celui-ci remplace le dernier état.

Les durées de formation peuvent être comprises entre quelques secondes, avec des projets de workflow d’orchestration et quelques heures, lorsque vous atteignez la [limite maximale](../service-limits.md) des énoncés. Avant la formation, vous aurez la possibilité d’activer l’évaluation, afin de voir comment votre modèle fonctionne. 

## <a name="train-model"></a>Effectuer l'apprentissage du modèle

Entrez un nouveau nom de modèle ou sélectionnez un modèle existant dans la liste déroulante **Nom du modèle**. Appuyez sur la touche Entrée après avoir ajouté un nom de modèle. Indiquez si vous souhaitez évaluer votre modèle en modifiant la bascule **Exécuter l’évaluation avec la formation**. Si cette option est activée, vos énoncés balisés seront divisés en 3 parties ; 80 % pour la formation, 10 % pour la validation et 10 % pour les tests. Par la suite, vous pourrez voir les résultats de l’évaluation du modèle.

:::image type="content" source="../media/train-model.png" alt-text="Capture d’écran montrant la page Effectuer l'apprentissage du modèle pour les projets de Language Understanding de conversation." lightbox="../media/train-model.png":::

Cliquez sur le bouton **Effectuer l'apprentissage** et attendez que celui-ci se termine. Vous verrez l’état de la formation de votre modèle dans la page d’affichage des détails du modèle.

## <a name="evaluate-model"></a>Évaluer le modèle

Une fois la formation du modèle terminé, vous pouvez afficher les détails de votre modèle et voir comment il s’exécute sur le jeu de test si vous avez activé l’évaluation à l’étape de la formation. L’observation de l’efficacité de votre modèle est appelée évaluation. Le jeu de test est composé de 20 % de vos énoncés et ce fractionnement est effectué de façon aléatoire avant la formation. Le jeu de test est constitué de données qui n’ont pas été introduites dans le modèle lors du processus de formation. Pour que le processus d’évaluation se termine, il doit y avoir au moins 10 énoncés dans votre jeu de formation.

Dans la page **d’affichage des détails du modèle**, vous pouvez voir tous vos modèles, avec leur état actuel et la date de la dernière formation.

:::image type="content" source="../media/model-page-1.png" alt-text="Capture d’écran montrant la page de détails du modèle pour les projets de Language Understanding utilisé dans la conversation." lightbox="../media/model-page-1.png":::

* Cliquez sur le nom du modèle pour plus de détails. Un nom de modèle est interactif uniquement si vous avez activé l’évaluation en amont. 
* Dans la section **Vue d’ensemble**, vous pouvez trouver la macro précision, le rappel et le score F1 pour les intentions ou les entités collectives, en fonction de l’option que vous sélectionnez. 
* Sous les onglets **Intentions** et **Entités**, vous pouvez trouver la micro précision, le rappel et le score F1 pour chaque intention ou entité séparément.

> [!NOTE]
> Si vous ne voyez pas les intentions ni entités que vous avez dans votre modèle affichées ici, cela est dû au fait qu’elles ne figuraient dans aucun des énoncés utilisés pour le jeu de test.

Vous pouvez afficher la [matrice de confusion](../concepts/evaluation-metrics.md#confusion-matrix) pour les intentions et les entités en cliquant sur l’onglet **Matrice de confusion des jeux de test** en haut de l’écran. 

## <a name="next-steps"></a>Étapes suivantes
* [Métriques d’évaluation de modèle](../concepts/evaluation-metrics.md)
* [Déployer et interroger le modèle](./deploy-query-model.md)
