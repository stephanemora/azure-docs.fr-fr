---
title: Guide pratique pour utiliser les actions d’attente et de non-attente avec un modèle Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser les actions d’attente et de non-attente avec un modèle Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: b09279e61ad60abcbda8b5bf576f5145ea8b9602
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239251"
---
# <a name="wait-and-non-wait-actions"></a>Actions d’attente et de non-attente

Ce tutoriel illustre la différence entre les actions d’attente et de non-attente dans Conversation Learner.

## <a name="video"></a>Vidéo

[![Aperçu du tutoriel 2](https://aka.ms/cl-tutorial-02-preview)](https://aka.ms/blis-tutorial-02)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails

- Action d’attente : une fois que le système a pris une action d’attente, il cesse de prendre des actions et attend l’entrée utilisateur.
- Action de non-attente : une fois que le système a pris une action de non-attente, il choisit immédiatement une autre action (sans attendre l’entrée utilisateur).

## <a name="steps"></a>Étapes

### <a name="create-a-new-model"></a>Créer un modèle

1. Dans l’interface utilisateur web, cliquez sur New Model
2. Dans Name, entrez WaitNonWait. Cliquez ensuite sur Create.

### <a name="create-the-first-wait-action"></a>Créer la première action d’attente

1. Cliquez sur Actions, puis sur New Action.
2. Dans Response, entrez « Which animal do you want ? ».
    - Il s’agit d’une action d’attente ; laissez la case Wait for Response cochée.
3. Cliquez sur Créer.

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

### <a name="train-the-bot"></a>Former le bot

1. Cliquez sur Train Dialogs, puis sur New Train Dialog.
2. Tapez « hello ».
3. Cliquez sur Score Actions et sélectionnez « Which animal do you want ? ».
4. Tapez « cow ».
5. Cliquez sur Score Actions et sélectionnez « Cows say moo ».
    - Le bot n’attend pas d’entrée et exécute l’action suivante.
2. Sélectionnez « Which animal do you want ? ».
3. Tapez « duck ».
5. Cliquez sur Score Actions et sélectionnez « Ducks say quack ».

![](../media/tutorial2_dialogs.PNG)

> [!NOTE]
> Remarquez la séquence de réponses du bot concernant les actions d’attente et de non-attente.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Présentation des entités](./3-introduction-to-entities.md)
