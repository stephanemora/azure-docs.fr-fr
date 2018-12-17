---
title: Intentions prédéfinies
titleSuffix: Azure Cognitive Services
description: LUIS inclut un ensemble d’intentions prédéfinies pour ajouter rapidement des scénarios courants d’utilisateur conversationnel.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 8fddbcf82bdbb052468b97754554da01bac7d82b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103725"
---
# <a name="add-prebuilt-intents-for-common-intents"></a>Ajouter des intentions prédéfinies pour les intentions courantes 

LUIS inclut un ensemble d’intentions prédéfinies à partir des domaines prédéfinis pour ajouter rapidement des intentions et des énoncés courants. Il s’agit d’un moyen simple et rapide d’ajouter des fonctionnalités à votre application cliente conversationnelle sans avoir à concevoir les modèles pour ces fonctionnalités. 

## <a name="add-a-prebuilt-intent"></a>Ajouter une intention prédéfinie

1. Sur la page **My Apps** (Mes applications), sélectionnez votre application. Vous ouvrez ainsi votre application à la section **Build** (Générer). 

1. Sur la page **Intents** (Intentions), sélectionnez **Add prebuilt intent** (Ajouter une intention prédéfinie) à partir de la barre d’outils au-dessus de la liste des intentions. 

1. Sélectionnez l’intention **Utilities.Cancel** à partir de la boîte de dialogue contextuelle. 

    ![Ajouter une intention prédéfinie](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Cliquez sur le bouton **Terminé**.

## <a name="train-and-test"></a>Effectuer l’apprentissage et tester

1. Après avoir ajouté l’intention, effectuez l’apprentissage de l’application en sélectionnant **Effectuer l’apprentissage** dans la barre d’outils en haut à droite. 

1. Testez la nouvelle intention en sélectionnant **Tester** dans la barre d’outils de droite. 

1. Dans la zone de texte, entrez des énoncés d’annulation :

    |Énoncé de test|Scores de prédictions|
    |--|:--|
    |Je souhaite annuler mon vol.|0.67|
    |Annuler l’achat.|0.52|
    |Annuler la réunion.|0.56|

    ![Tester l’intention prédéfinie](./media/luis-prebuilt-intents/test.png)

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Entités prédéfinies](./luis-prebuilt-entities.md)