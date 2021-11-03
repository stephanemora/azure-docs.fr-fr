---
title: Guide pratique pour envoyer un travail Compréhension du langage courant
titleSuffix: Azure Cognitive Services
description: Découvrez comment envoyer une demande pour Compréhension du langage courant.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: e1f53678ef99fb5477700628e14127e5572da816
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096565"
---
# <a name="deploy-and-test-model"></a>Déployer et tester le modèle

Une fois que vous avez [entraîné un modèle](./train-model.md) sur votre jeu de données, vous êtes prêt à le déployer. Après avoir déployé votre modèle, vous pouvez l’interroger pour obtenir des prédictions. 

## <a name="deploy-model"></a>Déployer un modèle

Le déploiement d’un modèle consiste à l’héberger et à le rendre disponible pour les prédictions via un point de terminaison. Vous ne pouvez avoir qu’un seul modèle déployé par projet. Si vous déployez un autre modèle, le modèle précédemment déployé sera remplacé.

Quand un modèle est déployé, vous pouvez tester le modèle directement dans le portail ou en appelant l’API qui lui est associée.

Sélectionnez simplement un modèle et cliquez sur Déployer le modèle dans la page Déployer le modèle. 

:::image type="content" source="../media/deploy-model.png" alt-text="Capture d’écran montrant la page de déploiement de modèle dans Language Studio." lightbox="../media/deploy-model.png":::

**Déploiements de projets de workflow d’orchestration**

Quand vous déployez un projet de workflow d’orchestration, une petite fenêtre s’affiche pour vous confirmer votre déploiement et configurer des paramètres pour les services connectés.

Si vous connectez une ou plusieurs applications LUIS, spécifiez le nom du déploiement et indiquez si vous utilisez un déploiement de type *emplacement* ou *version*.       
* Pour le type de déploiement *emplacement*, vous devez choisir entre un emplacement de production et un emplacement intermédiaire.
* Pour le type de déploiement *version*, vous devez spécifier la version que vous avez publiée.

Aucune configuration n’est nécessaire pour la réponse personnalisée aux questions et les connexions de Compréhension du langage courant, ou pour les intentions non liées.

Les projets LUIS **doivent être publiés** sur l’emplacement configuré lors du déploiement de l’orchestration, et les bases de connaissances de réponses aux questions personnalisées doivent également être publiés sur leurs emplacements de production.

:::image type="content" source="../media/deploy-connected-services.png" alt-text="Capture d’écran montrant l’écran de déploiement pour les projets de workflow d’orchestration." lightbox="../media/deploy-connected-services.png":::

## <a name="send-a-conversational-language-understanding-request"></a>Envoyer un travail Compréhension du langage courant

Une fois votre modèle déployé, vous pouvez commencer à utiliser le modèle déployé pour des prédictions. En dehors de la page Tester le modèle, vous pouvez commencer à appeler votre modèle déployé via des demandes d’API au point de terminaison personnalisé fourni. Cette demande de point de terminaison obtient les prédictions d’intentions et d’entités définies dans le modèle.

Vous pouvez obtenir l’URL complète de votre point de terminaison en accédant à la page **Déployer le modèle**, en sélectionnant votre modèle déployé, puis en cliquant sur « Obtenir l’URL de prédiction ».

:::image type="content" source="../media/prediction-url.png" alt-text="Capture d’écran montrant la demande et l’URL de prédiction" lightbox="../media/prediction-url.png":::

## <a name="api-response-for-a-conversations-project"></a>Réponse de l’API pour un projet de conversations

Dans un projet de conversations, vous obtenez des prédictions à la fois pour vos intentions et pour vos entités qui sont présentes dans votre projet. 
- Les intentions et les entités incluent un score de confiance entre 0,0 et 1,0 associé à la confiance du modèle pour la prédiction d’un certain élément dans votre projet. 
- L’intention avec le score le plus élevé est contenue dans son propre paramètre.
- Seules les entités prédites apparaissent dans votre réponse.
- Les entités indiquent :
    - Le texte de l’entité qui a été extraite
    - Le début de son emplacement indiqué par une valeur de décalage
    - La longueur du texte de l’entité indiquée par une valeur de longueur.

## <a name="api-response-for-an-orchestration-workflow-project"></a>Réponse de l’API pour un projet de workflow d’orchestration

Un projet de workflow d’orchestration est retourné avec la réponse de l’intention ayant le score le plus élevé et la réponse du service auquel il est connecté.
- Dans l’intention, le paramètre *targetKind* vous permet de déterminer le type de réponse qui a été retourné par l’intention principale de l’orchestrateur (conversation, LUIS ou QnA Maker).
- Vous obtiendrez la réponse du service connecté dans le paramètre *result*. 

Dans la demande, vous pouvez spécifier des paramètres supplémentaires pour chaque service connecté, dans le cas où l’orchestrateur route vers ce service.
- Dans les paramètres du projet, vous avez la possibilité de spécifier une autre requête pour le service connecté. Si vous ne spécifiez pas une autre requête, la requête d’origine est utilisée.
- Le paramètre *direct target* vous permet de contourner la décision de routage de l’orchestrateur et de cibler directement une intention connectée spécifique afin de forcer une réponse à celle-ci.

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de Compréhension du langage courant](../overview.md)
