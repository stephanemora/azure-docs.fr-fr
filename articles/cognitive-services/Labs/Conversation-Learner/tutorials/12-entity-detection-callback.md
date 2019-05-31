---
title: Guide pratique pour utiliser le rappel de détection d’entités avec un modèle Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser le rappel de détection d’entités avec un modèle Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: ed254ea3db1c8f0e680cba71185e9b4505d8f200
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387647"
---
# <a name="how-to-use-entity-detection-callback"></a>Comment utiliser le rappel de détection d’entités

Ce tutoriel montre le rappel de détection d’entités et illustre un modèle courant pour résoudre des entités.

## <a name="video"></a>Vidéo

[![Aperçu du tutoriel Rappel de Reconnaissance d’entités](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>Configuration requise
Pour les besoins de ce tutoriel, le bot `tutorialEntityDetectionCallback` doit être en cours d’exécution.

    npm run tutorial-entity-detection

## <a name="details"></a>Détails
Les rappels de Reconnaissance d’entité permettent de modifier le comportement de reconnaissance de l’entité et la manipulation de l’entité par le code. Nous démontrerons cette fonctionnalité en parcourant un modèle typique de conception de rappel de la détection d’entité. Par exemple, en associant « the big apple » (« la grosse pomme ») à « new york ».

## <a name="steps"></a>Étapes

### <a name="create-the-model"></a>Créer le modèle

1. Dans l’interface utilisateur web, cliquez sur « New Model ».
2. Dans le champ « Name » (« Nom »), tapez « EntityDetectionCallback » et appuyez sur Entrée.
3. Cliquez sur le bouton « Create » (« Créer »).

Trois entités sont nécessaires dans cet exemple, nous allons donc en créer trois.

### <a name="create-the-custom-trained-entity"></a>Créer l’entité personnalisée formée

1. Dans le volet gauche, cliquez sur « Entities » (« Entités »), puis sur le bouton « New Entity » (« Nouvelle entité »).
2. Sous « Entity Type » (« Type d’entité »), sélectionnez « Custom Trained » (« Formation personnalisée »).
3. Sous « Entity Name » (« Nom d’entité »), entrez « City » (« Ville »).
4. Cliquez sur le bouton « Create » (« Créer »).

### <a name="create-the-first-programmatic-entity"></a>Créer la première entité par programme

1. Cliquez sur le bouton « New Entity » (« Nouvelle entité »).
2. Sélectionnez « Programmatic » (« Programmation ») pour « Entity Type » (« Type d'entité »).
3. Sous « Entity Name » (« Nom d’entité »), entrez « CityUnknown ».
4. Cliquez sur le bouton « Create » (« Créer »).

### <a name="create-the-first-programmatic-entity"></a>Créer la première entité par programme

1. Cliquez sur le bouton « New Entity » (« Nouvelle entité »).
2. Sélectionnez « Programmatic » (« Programmation ») pour « Entity Type » (« Type d'entité »).
3. Sous « Entity Name » (« Nom d’entité »), entrez « CityUResolved ».
4. Cliquez sur le bouton « Create » (« Créer »).

Créez à présent trois actions.

### <a name="action-creation"></a>Création de l’action

1. Dans le panneau gauche, cliquez sur « Actions », puis sur le bouton « New Action » (Nouvelle Action).
2. Dans « Bot’s response... » (« Réponse du bot... »), tapez « Which city do you want? » (« Quelle ville souhaitez-vous ? »).
3. Dans le champ « Expected Entity in User reply... » (« Entité attendue dans la réponse de l’utilisateur »), tapez « City » (« Ville »).
4. Dans le champ « Disqualifying Entities » (« Entités disqualifiantes »), tapez « City » (« Ville »).
5. Cliquez sur le bouton « Create » (« Créer »).
6. Cliquez sur le bouton « New Action » (« Nouvelle action »).
7. Dans « Bot’s response... » (« Réponse du bot... »), tapez « Which city do you want? » (« Quelle ville souhaitez-vous ? »).
8. Dans le champ « Expected Entity in User reply... » (« Entité attendue dans la réponse de l’utilisateur »), tapez « I don’t know this city » (« Je ne connais pas cette ville »).
9. Cliquez sur le bouton « Create » (« Créer »).
10. Cliquez sur le bouton « New Action » (« Nouvelle action »).
11. Dans le champ « Bot’s response... » (« Réponse du Bot »), tapez « $CityResolved is very nice » (« $CityResolved est une très belle ville »).
12. Cliquez sur le bouton « Create » (« Créer »).

Voici le code de rappel :

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>Former le modèle

1. Dans le volet gauche, cliquez sur « Train Dialogs » (« Boîtes de dialogue d’apprentissage »), puis le bouton « New Train Dialog » (« Nouveau dialogue d’apprentissage »).
2. Dans le panneau de conversation, sous « Type your message... » (« Entrez votre message... »), tapez « hi » (« Bonjour »).
3. Cliquez sur le bouton « Score Actions ».
4. Sélectionnez la réponse, « Which city do you want ? » (« Quelle ville voulez-vous ? »)
5. Dans le volet de la conversation, intitulé « Type your message... » (« Tapez votre message »), tapez « big apple »
6. Cliquez sur le bouton « Score Actions ».
    - Cliquer sur le bouton déclenche le rappel de détection d’entité
    - Le code de rappel définit correctement la valeur d’entité CityResolved sur « new york ».
7. Sélectionnez la réponse, « new york is very nice » (« new york est magnifique »).

Ce modèle est typique de nombreux scénarios de bot. Les énoncés de l’utilisateur et les entités extraites sont fournis à votre logique métier, laquelle transforme l’énoncé en forme canonique, qui est ensuite enregistrée en entités programmatiques, pour les tours suivants de la boîte de dialogue.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Rappels de session](./13-session-callbacks.md)
