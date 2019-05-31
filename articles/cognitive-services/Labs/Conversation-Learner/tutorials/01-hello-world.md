---
title: Création d’un modèle Apprenant de conversation Learner « Hello World » - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment créer un modèle Apprenant de conversation « Hello World ».
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: fe5d21fadef8f4452ba36259dbf89cefc78230de
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388056"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Création d’un modèle « Hello World » avec Apprenant de conversation

Ce didacticiel montre comment prendre en main Conversation Learner, notamment la création d’actions, l’apprentissage interactif du bot et l’apport de corrections aux dialogues journalisés avec des utilisateurs finaux.

## <a name="video"></a>Vidéo

[![Didacticiel Hello World en préversion](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>Configuration requise
Si ce n’est déjà fait, vérifiez d’abord que toutes les étapes d’installation sont terminées, notamment la création d’un fichier `.env` avec votre clé de création LUIS.  Consultez [Démarrage rapide](../quickstart.md) pour plus d’informations.

Ce didacticiel nécessite que le bot didacticiel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="steps"></a>Étapes

Démarrez sur la page d’accueil de l’interface utilisateur web.

### <a name="create-the-model"></a>Créer le modèle
1. Cliquez sur le bouton « New Model » (Nouveau modèle).
2. Dans le champ « Name » (Nom), entrez « Hello World ».
3. Cliquez sur le bouton « Create » (Créer).

Vous devrez maintenant voir l’affichage du modèle que vous avez créé.

### <a name="create-an-action"></a>Créer une action
1. Dans le volet gauche, cliquez sur « Actions », puis sur le bouton « New Action » (Nouvelle Action).
    - Une action peut être un message texte que Conversation Learner retourne à l’utilisateur, un appel d’API ou une carte.
2. Dans le champ « Bot’s response... » (Réponse du bot...), entrez « Bonjour ».
    - Il s’agit de la réponse que le bot retournera.
3. Cliquez sur le bouton « Create » (Créer).

Vous avez créé la première action que le bot peut exécuter, à savoir retourner une réponse textuelle.

### <a name="train-dialogs"></a>Boîtes de dialogue d’apprentissage
C’est ici que vous formez le modèle sur la façon de répondre aux énoncés de l’utilisateur.

#### <a name="first-training-dialog"></a>Premier dialogue d’apprentissage

1. Dans le volet gauche, cliquez sur « Train Dialogs » (Dialogues d’apprentissage), puis sur le bouton « New Train Dialog » (Nouveau dialogue d’apprentissage).
2. Tapez « Salut », puis appuyez sur Entrée.
    - En tant qu’exemple de ce que l’utilisateur pourrait dire au début d’une conversation.
3. Cliquez sur le bouton « Sore actions » (Noter les actions).
4. Sélectionnez « Hello » (Bonjour).
    - Vous venez de terminer un tour complet de cet exemple de dialogue. 
5. Tapez la réponse de l’utilisateur, « Au revoir ».
6. Cliquez sur le bouton « Sore actions » (Noter les actions).
7. Cliquez sur le bouton «+ Action ».
8. Tapez « Goodbye! » (Au revoir !) dans le champ « Bot’s response... » (Réponse du bot), puis cliquez sur le bouton « Create » (Créer).
    - Notez que le bot a répondu avec l’action que vous venez de créer.
9. Cliquez sur le bouton « Save » (Enregistrer). 
    - Cela a pour effet de terminer et d’enregistrer ce dialogue d’apprentissage.

Vous disposez maintenant d’un dialogue d’apprentissage dans le modèle, ainsi que d’une entité et de deux actions.

#### <a name="second-training-dialog"></a>Deuxième dialogue d’apprentissage
Effectuons un apprentissage supplémentaire pour voir comment le bot répond.

1. Cliquez sur le bouton « New Train Dialog » (Nouveau dialogue d’apprentissage).
2. Tapez « Bonjour ».
    - Ceci est similaire au premier dialogue et nous espérons obtenir un bon score de la part du bot.
3. Cliquez sur le bouton « Sore actions » (Noter les actions).
    - Il se peut que la position et le score ne soient toujours pas suffisamment précis et nécessitent un apprentissage supplémentaire.
4. Sélectionnez « Hello ».
5. Tapez la réponse de l’utilisateur « bye ».
6. Cliquez sur le bouton « Sore actions » (Noter les actions).
7. Sélectionnez « Au revoir ! ».
8. Cliquez sur le bouton « Save » (Enregistrer).

### <a name="log-dialogs"></a>Dialogues journalisés
C’est ici que vous testez, affichez et corrigez les conversations que vous ou des utilisateurs réels ont eues avec votre bot.

#### <a name="test-the-model-as-an-end-user"></a>Tester le modèle en tant qu’un utilisateur final
1. Dans le volet gauche, cliquez sur « Log Dialogs » (Dialogues journalisés), puis sur le bouton « New Log Dialog » (Nouveau dialogue journalisé).
2. Tapez « Bonjour ».
3. Attendez quelques instants. Le bot devrait répondre automatiquement « Hello ».
4. Entrez « bye bye ».
5. Attendez quelques instants. Le bot devrait de nouveau répondre automatiquement « Hello ».
6. Cliquez sur le bouton « Done Testing » (Test terminé).

#### <a name="view-and-correct-a-user-conversation"></a>Afficher et corriger une conversation d’utilisateur
À l’aide des dialogues journalisés, vous pouvez afficher la liste des conversations que des utilisateurs ont eues avec votre bot. Vous pouvez également les modifier afin de corriger les réponses du bot et enregistrer les interactions en tant que dialogues d’apprentissage. Pour ce faire :
1. Dans la grille, cliquez sur le journal de la conversation.
2. Cliquez sur la dernière action du bot, par exemple « Hello » (Bonjour).
3. Sélectionnez « Au revoir ! ». pour corriger le bot.
4. Cliquez sur le bouton « Save As Train Dialog » (Enregistrer en tant que dialogue d’apprentissage).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Présentation de l’apprentissage](./02-intro-to-training.md)
