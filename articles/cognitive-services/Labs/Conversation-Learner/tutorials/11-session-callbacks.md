---
title: Guide pratique pour utiliser les rappels de session avec un modèle Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser les rappels de session avec un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 73fb2a3f10476550bbe6bd20c387a1cc2a7727d8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246478"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Guide pratique pour utiliser les rappels de session avec un modèle Conversation Learner

Ce didacticiel illustre les rappels onSessionStart et onSessionEnd.

## <a name="video"></a>Vidéo

[![Aperçu du tutoriel 11](https://aka.ms/cl-tutorial-11-preview)](https://aka.ms/blis-tutorial-11)

## <a name="requirements"></a>Configuration requise
Pour les besoins de ce tutoriel, le bot `tutorialSessionCallbacks` doit être en cours d’exécution.

    npm run tutorial-session-callbacks

## <a name="details"></a>Détails
Ce didacticiel décrit le concept d’une session, comment les sessions sont gérées par défaut et comment vous pouvez remplacer ce comportement.

Une session est une conversation avec le bot. Il peut comporter plusieurs rotations, mais sans arrêts longs dans la conversation (par exemple, 30 minutes).  Consultez la page d’aide sur les « Limites » pour la durée du délai d’expiration de la session par défaut.

S’il existe des arrêts longs, le bot passe à sa session suivante.  Démarrer une nouvelle session place le réseau neuronal récurrent dans son état initial.  Par défaut, ceci efface également toutes les valeurs d’entité, bien que ce comportement puisse être modifié (illustré ci-dessous).

### <a name="open-the-demo"></a>Ouvrir la démonstration

Dans la liste des modèles, cliquez sur Tutorial-11-SessionCallbacks. 

### <a name="entities"></a>Entités

Quatre entités sont définies dans le modèle.

![](../media/tutorial11_entities.PNG)

Une information importante est que BotName est une entité de programmation.  Cette entité sera définie par le bot au démarrage de la session.

### <a name="actions"></a>Actions

Quatre actions sont définies dans le modèle.

![](../media/tutorial11_actions.PNG)

Ce didacticiel décrira tout d’abord comment contrôler les valeurs d’entité au début de la session, par exemple, la définition de l’entité BotName avant tout indication de l’utilisateur.

Ce didacticiel montrera ensuite comment rendre les valeurs d’une session persistantes dans la suivante.  Dans ce didacticiel, nous supposons que le nom d’utilisateur et le numéro de téléphone ne changent pas d’une session à l’autre, mais que leur emplacement peut changer.  Par conséquent, nous conservons le nom et le numéro de téléphone entre les sessions, mais nous effaçons l’emplacement de l’utilisateur.

### <a name="train-dialog"></a>Boîte de dialogue d’apprentissage

Voici un exemple de boîte de dialogue. Il s’agit d’une session, à savoir qu’il n’y a pas d’arrêts longs dans cette boîte de dialogue.

![](../media/tutorial11_traindialog.PNG)

### <a name="code-for-the-callbacks"></a>Code pour les rappels

Le code pour les méthodes de rappel se trouve dans le fichier C: c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial11_code.PNG)

Ces deux méthodes sont facultatives.

- OnSessionStartCallback : cette méthode définit l’entité BotName.
- OnSessionEndCallback : vous pouvez spécifier ce que vous voulez conserver. Cela effacera toutes les entités sauf le nom d’utilisateur et le téléphone de l’utilisateur.

### <a name="try-the-bot"></a>Essayer le bot

Connectez-vous à l’interface utilisateur web, puis cliquez sur Log Dialogs (Boîtes de dialogue du journal).

1. Entrez « hello ».
2. Système : « Bonjour, je m’appelle Botty. Quel est votre nom ? » qui porte le nom Botty provenant de OnSessionStartCallback.
3. Entrez « jason ».
4. Système : «Bonjour jason. Quel est votre numéro de téléphone ? »
5. Entrez « 555-555-5555 ».
6. Système : « Pouvez-vous indiquer à Botty votre emplacement Jason ? »
7. Tapez « Redmond ».

Il s’agit d’une session. Pour démarrer une nouvelle session, nous devons mettre fin à cette session. 

1. Cliquez sur Délai d’expiration de session. Cela vous amène à la session suivante.
    - Le bouton « Délai d’expiration de session » est fourni à des fins de débogage.  Dans une session réelle, une longue pause d’environ 30 minutes devrait se produire.  Consultez la page d’aide sur les « Limites » pour la durée du délai d’expiration de la session par défaut.
1. Entrez « Bonjour ».
2. Système : « Pouvez-vous indiquer à Botty votre emplacement Jason ? »
    - Le système a mémorisé le nom et le numéro de téléphone.
2. Entrez un nouvel emplacement : « Seattle ».
3. Système : « Vous êtes donc à Seattle Jason ».
4. Cliquez sur Tests terminés.

Revenons aux boîtes de dialogue du journal. Notez que la dernière conversation est divisée en deux parties car chaque boîte de dialogue du journal correspond à une session.  

![](../media/tutorial11_splitdialogs.PNG)

- Dans la première interaction, Botty est défini, mais le nom et le numéro de téléphone ne le sont pas.
- La deuxième interaction affiche le nom et le numéro de téléphone.

Vous savez maintenant comment les sessions sont gérées par défaut et comment vous pouvez remplacer le comportement par défaut. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Appels d’API](./12-api-calls.md)
