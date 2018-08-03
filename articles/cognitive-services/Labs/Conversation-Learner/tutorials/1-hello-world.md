---
title: Création d’un modèle Apprenant de conversation Learner « Hello World » - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment créer un modèle Apprenant de conversation « Hello World ».
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 70b8f25bd699cbdb069892d65bf766ef3953f59d
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170869"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Création d’un modèle « Hello World » avec Apprenant de conversation

Ce tutoriel montre comment prendre en main Conversation Learner, notamment la création d’actions, l’apprentissage interactif et les corrections apportées aux dialogues enregistrés provenant des utilisateurs finaux.

## <a name="video"></a>Vidéo

[![Aperçu du didacticiel 1](http://aka.ms/cl-tutorial-01-preview)](http://aka.ms/blis-tutorial-01)


## <a name="requirements"></a>Configuration requise
Si ce n’est déjà fait, vérifiez d’abord que toutes les étapes d’installation sont terminées, notamment la création d’un fichier `.env` avec votre clé de création LUIS.  Consultez [Démarrage rapide](https://github.com/Microsoft/ConversationLearner-Samples) pour plus d’informations.

Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="steps"></a>Étapes

Démarrez sur la page d’accueil de l’interface utilisateur web.

### <a name="create-the-model"></a>Création du modèle
1. Cliquez sur Nouveau modèle
2. Dans le champ Nom, entrez Hello World
3. Click Create

### <a name="create-an-action"></a>Créer une action

1. Cliquez sur le modèle Hello World pour le démarrer
2. Cliquez sur Actions, puis sur Nouvelle action
    - Une action peut être un message texte que Conversation Learner retourne à l’utilisateur, un appel d’API ou une carte.
3. En réponse, tapez « Hello World ! »
    - Ceci est la réponse que le bot retourne
4. Click Create

Vous avez créé la première action que le bot peut exécuter, à savoir retourner une réponse texte.

### <a name="train-the-bot"></a>Former le bot

#### <a name="create-the-first-dialog"></a>Créer le premier dialogue

1. Cliquez sur Dialogues d’apprentissage, puis sur Nouveau dialogue d’apprentissage
2. Entrez un exemple de ce que l’utilisateur utilisera au début de la conversation, par exemple, « hello ».
3. Cliquez sur Actions de score
4. Sélectionnez « Hello World ! »
    - Cette opération crée un exemple de dialogue. 
2. Entrez « goodbye »
3. Cliquez sur Actions de score
4. Cliquez sur Ajouter une action, puis entrez « Goodbye ! » en réponse, puis cliquez sur « Créer »
5. Cliquez sur Apprentissage terminé. Ceci met fin à ce dialogue d’apprentissage.

Vous avez maintenant un dialogue d’apprentissage dans le système.

#### <a name="continue-teaching-the-bot"></a>Continuer l’apprentissage du bot
Effectuons un autre apprentissage pour voir comment le bot répond.

1. Cliquez sur Nouveau dialogue d’apprentissage
2. Entrez « hi there »
    - Ceci est similaire au premier dialogue et nous espérons obtenir un bon score de la part du bot.
2. Cliquez sur Action de score
    - La position et le score peuvent ne pas être encore suffisamment précis et nécessiter un apprentissage supplémentaire.
3. Cliquez sur Sélectionner en regard de « Hello World ! »
4. Puis, entrez « bye »
5. Cliquez sur Actions de score
6. Sélectionnez « Goodbye ! »
7. Cliquez sur Apprentissage terminé

![](../media/tutorial1_actions.PNG)

Nous allons effectuer une autre session d’apprentissage pour voir comment le bot fonctionne.

Répétez les étapes ci-dessus en utilisant « hi » et « byebye », et notez les changements de position et de score dans la réponse du bot lorsque vous cliquez sur Action de score.

Vous pouvez maintenant répéter les étapes en utilisant « howdy » et « good bye ». Vous remarquerez que les scores sont améliorés, ce qui indique que le bot a appris cette interaction.

![](../media/tutorial1_dialogs.PNG)

### <a name="test-the-bot-as-an-end-user"></a>Tester le robot en tant qu’utilisateur final

1. Cliquez sur Dialogues de journal, puis sur Nouveau dialogue de journal
2. Tapez « hello there »
3. Puis, « bye »

Vous pouvez également essayer de démarrer une conversation avec « bye » et noter la réponse du bot.

### <a name="view-conversations-in-the-log-dialogs"></a>Afficher des conversations dans les Dialogues de journal

Dans Dialogues de journal, vous pouvez afficher la liste des conversations, mettre à jour et enregistrer les interactions en tant que dialogues d’apprentissage. Pour ce faire :

1. Cliquez sur le journal d’une conversation
2. Si la conversation semble correcte, cliquez sur la dernière action, par exemple « Goodbye ».
3. Cliquez pour sélectionner la réponse suggérée. 
    - Vous pouvez également sélectionner ou ajouter une autre action.
4. Puis, cliquez sur Terminé pour l’enregistrer en tant que dialogue d’apprentissage.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Actions d’attente et actions de non-attente](./2-wait-vs-nonwait-actions.md)