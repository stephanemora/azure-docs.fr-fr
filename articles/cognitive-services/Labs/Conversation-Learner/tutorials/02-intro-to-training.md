---
title: Présentation de l’apprentissage d’un modèle Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment effectuer l’apprentissage d’un modèle, notamment la création de branches et la modification d’un apprentissage précédent via Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 0bf5b71a4b0f51a586febbdaeaf8caba03c5b25a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387916"
---
# <a name="introduction-to-training"></a>Présentation de l’apprentissage

Ce tutoriel montre les principes fondamentaux de l’apprentissage d’un modèle, de la création d’une nouvelle branche d’apprentissage basée sur un apprentissage précédent et de la modification de la réponse d’un bot.

## <a name="video"></a>Vidéo

[![Préversion du tutoriel de présentation de l’apprentissage](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails

- Actions : Une réponse de bot aux entrées de l’utilisateur.
- Apprentissage : La façon dont nous apprenons à un bot à répondre à une entrée de l’utilisateur.
- Création de branches : modification d’une entrée utilisateur dans un dialogue d’apprentissage enregistré à des fins de création d’un nouveau dialogue d’apprentissage qui démarre de la même manière que l’original, mais emmène la conversation dans une autre direction.

## <a name="steps"></a>Étapes

### <a name="create-a-new-model"></a>Créer un modèle

1. Dans l’interface utilisateur web, cliquez sur New Model
2. Dans Name (Nom), entrez « Inspirer un bot ». Cliquez ensuite sur Créer.

### <a name="create-an-action"></a>Créer une action

1. Dans le volet gauche, cliquez sur Actions, puis sur le bouton New Action (Nouvelle Action).
2. Dans le champ Bot's response (Réponse du bot), entrez « Bonjour ! Voulez-vous trouver l’inspiration aujourd’hui ? ».
    - Conservez les valeurs par défaut des autres champs et cases à cocher.
3. Cliquez sur Créer.

### <a name="first-training-and-creating-another-action-while-training"></a>Premier apprentissage et création d’une autre action pendant l’apprentissage

1. Dans le volet gauche, cliquez sur Train Dialogs (Dialogues d’apprentissage), puis le bouton New Train Dialog (Nouveau dialogue d’apprentissage).
2. Dans le panneau de chat, sous Type your message... (Entrez votre message...), tapez « Hello ». 
    - Cela simule la conversation du côté de l’utilisateur.
3. Cliquez sur Score Actions (Actions de score).
4. Sélectionnez la réponse « Bonjour ! Voulez-vous trouver l’inspiration aujourd’hui ? ».
5. Répondez « Oui » en prenant le rôle de l’utilisateur.
6. Cliquez sur Score Actions (Actions de score).
7. Cliquez sur le bouton +Action. 
    - Vous êtes dirigé vers la boîte de dialogue familière Create an Action (Créer une action).
8. Tapez la réponse du bot « Vous êtes génial ! »
9. Cliquez sur Créer.
10. Notez que le bot répond immédiatement.
11. Cliquez sur le bouton Save (Enregistrer).

### <a name="branch-a-second-training-off-of-the-first-training"></a>Créer une deuxième branche d’apprentissage à partir du premier apprentissage
1. Cliquez sur la ligne de grille qui résume le premier apprentissage. 
    - Ainsi, vous pouvez afficher et modifier l’apprentissage existant.
2. Cliquez sur la réponse utilisateur « Oui ». 
    - Cela expose des contrôles d’édition.
3. Cliquez sur l’icône en forme de branche. 
    - Cela a pour effet d’ouvrir une invite pour une entrée utilisateur différente pour une nouvelle conversation.
4. Entrez « non », appuyez sur Entrée ou cliquez sur le bouton Create (Créer). 
    - À ce stade, vous disposez d’une nouvelle instance d’un dialogue d’apprentissage. Celui d’origine reste inchangé.
5. Cliquez sur Score Actions (Actions de score).
6. Cliquez sur la réponse incorrecte du bot qui vient de s’afficher.
7. Cliquez sur le bouton +Action. 
    - Vous pouvez ainsi créer une action avec laquelle le bot peut répondre.
8. Tapez la réponse du bot comme suit : « Aucun problème ! Bonne journée ! »
9. Cliquez sur Create.
10. Notez que le bot répond immédiatement.
11. Cliquez sur le bouton Save (Enregistrer).

### <a name="test-the-trainings"></a>Tester les apprentissages
1. Dans le volet de gauche, cliquez sur Log Dialogs (Dialogues de journal), puis sur New Log Dialog (Nouveau dialogue de journal).
2. Tapez le message « Bonjour ». 
3. Notez que le bot répond automatiquement de la façon dont vous l’avez entraîné.
4. Tapez la réponse de l’utilisateur « Oui ».
5. Notez la réponse du bot qui montre que le premier apprentissage fonctionne.
6. Cliquez sur le bouton Session Timeout (Délai d’expiration de session). Cela indique à Conversation Learner que vous souhaitez recommencer, en ignorant les échanges de conversations qui viennent d’avoir lieu.
7. Tapez le message « Bonjour ». 
8. Notez que le bot répond automatiquement de la façon dont vous l’avez entraîné.
9. Tapez la réponse de l’utilisateur « Non ».
10. Notez la réponse du bot qui montre que le deuxième apprentissage fonctionne.
11. Cliquez sur le bouton Done Testing (Test terminé).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Actions d’attente et actions de non-attente](./03-wait-vs-nonwait-actions.md)
