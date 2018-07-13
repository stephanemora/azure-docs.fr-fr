---
title: Guide pratique pour utiliser les actions d’attente et de non-attente avec une application Conversation Learner - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser les actions d’attente et de non-attente avec une application Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bb2cbd55b6c8be51213095926bb592169613d1fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369552"
---
# <a name="wait-and-non-wait-actions"></a>Actions d’attente et de non-attente

Ce tutoriel illustre la différence entre les actions d’attente et de non-attente dans Conversation Learner.

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot de tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails

- Action d’attente : une fois que le système a pris une action d’attente, il cesse de prendre des actions et attend l’entrée utilisateur.
- Action de non-attente : une fois que le système a pris une action de non-attente, il choisit immédiatement une autre action (sans attendre l’entrée utilisateur).

## <a name="steps"></a>Étapes

### <a name="create-a-new-app"></a>Créer une application

1. Dans l’interface utilisateur web, cliquez sur New App.
2. Dans Name, entrez WaitNonWait. Cliquez ensuite sur Create.

### <a name="create-the-first-wait-action"></a>Créer la première action d’attente

1. Cliquez sur Actions, puis sur New Action.
2. Dans Response, entrez « Which animal do you want ? ».
    - Il s’agit d’une action d’attente ; laissez la case Wait for Response cochée.
3. Cliquez sur Done.

### <a name="create-a-non-wait-action"></a>Créer une action de non-attente

1. Cliquez sur New Action.
2. Dans Response, tapez « Cows say moo ».
3. Décochez la case Wait for Response.
4. Cliquez sur Create.

### <a name="create-a-second-non-wait-action"></a>Créer une deuxième action de non-attente

1. Cliquez sur New Action.
2. Dans Response, tapez « Ducks say quack ».
3. Décochez la case Wait for Response.
4. Cliquez sur Create.

![](../media/tutorial2_actions.PNG)

### <a name="train-the-bot"></a>Entraîner le bot

1. Cliquez sur Train Dialogs, puis sur New Train Dialog.
2. Tapez « hello ».
3. Cliquez sur Score Actions et sélectionnez « Which animal do you want ? ».
4. Tapez « cow ».
5. Cliquez sur Score Actions et sélectionnez « Cows say moo ».
    - Notez que le bot n’attend pas d’entrée et exécute l’action suivante.
2. Sélectionnez « Which animal do you want ? ».
3. Tapez « duck ».
5. Cliquez sur Score Actions et sélectionnez « Ducks say quack ».

![](../media/tutorial2_dialogs.PNG)

Notez la séquence des réponses du bot qui concernent les actions d’attente et de non-attente.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Présentation des entités](./3-introduction-to-entities.md)
