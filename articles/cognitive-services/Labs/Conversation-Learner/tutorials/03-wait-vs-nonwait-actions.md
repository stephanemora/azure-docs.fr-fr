---
title: Guide pratique pour utiliser les actions d’attente et de non-attente avec un modèle Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser les actions d’attente et de non-attente avec un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2869e6c59388adf548e5f239d3a831a5a3f060dd
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214260"
---
# <a name="wait-and-non-wait-actions"></a>Actions d’attente et de non-attente

Ce tutoriel illustre la différence entre les actions d’attente et de non-attente dans Conversation Learner.

## <a name="video"></a>Vidéo

[![Didacticiel sur l’attente et la non-attente en préversion](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails

- Action d'attente : une fois que le système a opté pour une action d'attente, il cesse d'agir et attend une entrée utilisateur.
- Action de non-attente : une fois que le système a opté pour une action de non-attente, il choisit immédiatement une autre action (sans attendre d'entrée utilisateur).

## <a name="steps"></a>Étapes

### <a name="create-a-new-model"></a>Créer un modèle

1. Dans l’interface utilisateur web, cliquez sur New Model
2. Dans le champ « Name » (Nom), tapez « Wait Non-Wait » (Attente Non-Attente), puis appuyez sur Entrée ou cliquez sur le bouton « Create » (Créer).

### <a name="create-the-first-two-wait-actions"></a>Créer les deux premières actions d’attente

1. Dans le volet gauche, cliquez sur « Actions », puis sur le bouton « New Action » (Nouvelle Action).
2. Dans le champ « Bot’s response... » (Réponse du bot...), entrez « Quelle pizza vous ferait plaisir ? ».
    - Comme il s’agit d’une action d’attente, laissez la case « Wait for Response » (Attendre une réponse) activée.
3. Cliquez sur le bouton « Create » (Créer).
4. En répétant ces étapes, créez une autre action avec « La pizza est en route ! » en tant que réponse du bot.

### <a name="train-using-those-wait-actions"></a>Fromer à l’aide de ces actions d’attente

1. Dans le volet gauche, cliquez sur « Train Dialogs » (Dialogues d’apprentissage), puis sur le bouton « New Train Dialog » (Nouveau dialogue d’apprentissage).
2. Dans le panneau de conversation, sous « Type your message... » (Tapez votre message...), entrez « Bonjour ». 
    - Cela simule le côté utilisateur de la conversation.
3. Cliquez sur le bouton « Sore actions » (Noter les actions).
4. Sélectionnez la réponse « Quelle pizza vous ferait plaisir ? ».
5. En vous mettant à la place d’un utilisateur, répondez « Margherita ».
6. Cliquez sur le bouton « Sore actions » (Noter les actions).
7. Sélectionnez la réponse « La pizza est en route ! ».
8. Cliquez sur le bouton « Save » (Enregistrer).

### <a name="create-a-non-wait-action-while-training"></a>Créer une action de non-attente pendant la formation
Si vous pouvez créer l’action de non-attente comme vous l’avez fait précédemment, vous pouvez également la créer à partir d’une session d’apprentissage.
1. Cliquez sur le bouton « New Train Dialog » (Nouveau dialogue d’apprentissage).
2. En tant vous mettant à la place d’un utilisateur, tapez « Bonjour ».
3. Cliquez sur le bouton « Sore actions » (Noter les actions).
4. Cliquez sur le bouton « + Action ». 
    - Vous êtes dirigé vers la boîte de dialogue familière « Create an Action » (Créer une action).
5. Tapez la réponse du robot « Bienvenue au Bot Pizza ! ».
6. Désactivez la case à cocher « Wait for Response » (Attendre une réponse).
7. Cliquez sur le bouton « Create » (Créer).
    - Notez que le bot répond immédiatement « Bienvenue au Bot Pizza ! » et que vous êtes à nouveau invité à entrer une autre réponse du bot. C’est parce que la réponse du bot était l’action de non-attente que nous venons de créer.
9. Sélectionnez la réponse « Quelle pizza vous ferait plaisir ? ».
10. En vous mettant à la place d’un utilisateur, répondez « Margherita ».
11. Cliquez sur le bouton « Sore actions » (Noter les actions).
12. Sélectionnez la réponse « La pizza est en route ! ».
13. Cliquez sur le bouton « Save » (Enregistrer).

> [!NOTE]
> Remarquez la séquence de réponses du bot concernant les actions d’attente et de non-attente.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Présentation des entités](./04-introduction-to-entities.md)
