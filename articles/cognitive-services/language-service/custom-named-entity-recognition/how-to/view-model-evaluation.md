---
title: Afficher l’évaluation d’un modèle de Reconnaissance d’entité nommée (NER) personnalisée
titleSuffix: Azure Cognitive Services
description: Découvrez comment afficher les scores d’évaluation pour un modèle de Reconnaissance d’entité nommée (NER) personnalisée
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 62724137bd02f8c3ff58665ad92a4346eddc0b53
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097922"
---
# <a name="view-the-models-evaluation-and-details"></a>Afficher l’évaluation et les détails du modèle

Une fois l’entraînement de votre modèle terminé, vous pouvez afficher les détails du modèle et voir quelles sont ses performances avec le jeu de test, qui contient 10 % de vos données au hasard, qui est créé au cours de l’[entraînement](train-model.md#data-split). Le jeu de test est constitué de données qui n’ont pas été introduites dans le modèle pendant le processus d’entraînement. Pour que le processus d’évaluation se termine, il doit y avoir au moins 10 fichiers dans votre jeu de données. Vous devez également avoir un [projet de NER personnalisée](../quickstart.md) avec un [modèle entraîné](train-model.md).

## <a name="prerequisites"></a>Prérequis

* Un [projet créé](create-project.md) correctement avec un compte de stockage Blob Azure configuré
    * Des données textuelles qui [ont été chargées](create-project.md#prepare-training-data) dans votre compte de stockage.
* Des [données étiquetées](tag-data.md)
* Un [modèle correctement entraîné](train-model.md)

Pour plus d’informations, consultez le [cycle de vie du développement d’applications](../overview.md#application-development-lifecycle).

## <a name="view-the-models-evaluation-details"></a>Afficher les détails de l’évaluation du modèle

1. Accédez à la page de votre projet dans [Langage Studio](https://aka.ms/languageStudio).
    1. Recherchez dans Language Studio la section intitulée **Extract information**.
    2. Sélectionnez **Custom named entity extraction**.

2. Sélectionnez **View model details** dans le menu du côté gauche de l’écran.

3. Visualisez l’état de votre entraînement de modèle dans la colonne **Status** et le score F1 du modèle dans la colonne **F1 score**. Vous pouvez cliquer sur le nom du modèle pour plus d’informations.

4. Vous pouvez trouver les métriques d’évaluation **au niveau du modèle** sous **Overview**, ainsi que les métriques d’évaluation **au niveau de l’entité** sous **Entity performance metrics**. La matrice de confusion pour le modèle se trouve sous **Test set confusion matrix**
    
    > [!NOTE]
    > Si vous ne trouvez pas toutes les entités affichées ici, cela est dû au fait qu’elles ne figuraient dans aucun des fichiers du jeu de test.

    :::image type="content" source="../media/model-details.png" alt-text="Capture d’écran des métriques de performance de modèle dans Language Studio" lightbox="../media/model-details.png":::

## <a name="next-steps"></a>Étapes suivantes

* Après avoir examiné l’évaluation de votre modèle, vous pouvez commencer à [améliorer votre modèle](improve-model.md).
* Apprenez-en davantage sur les [métriques utilisées dans l’évaluation](../concepts/evaluation-metrics.md). 
