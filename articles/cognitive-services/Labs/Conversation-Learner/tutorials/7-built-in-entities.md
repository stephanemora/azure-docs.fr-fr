---
title: Comment ajouter des entités préintégrées à un modèle application Apprenant de conversation - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment ajouter des entités préintégrées à un modèle Apprenant de conversation.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 84d73add5586aaaf130253a8122a4152e39bcbe9
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171365"
---
# <a name="how-to-add-pre-built-entities"></a>Comment ajouter des entités préintégrées
Ce didacticiel explique comment ajouter des entités « préintégrées » à votre modèle Apprenant de conversation.

## <a name="video"></a>Vidéo

[![Aperçu du didacticiel 7](http://aka.ms/cl-tutorial-07-preview)](http://aka.ms/blis-tutorial-07)

## <a name="requirements"></a>Configuration requise
Ce tutoriel nécessite que le bot tutoriel général soit en cours d’exécution.

    npm run tutorial-general

## <a name="details"></a>Détails

Les entités préintégrées reconnaissent les types classiques d’entités, telles que les nombres, les dates, les montants monétaires, etc.  Contrairement aux entités personnalisées, elles sont « prêtes à l’emploi » et ne nécessitent pas de formation.  Contrairement aux entités personnalisées, leur comportement ne peut pas être modifié.  Par défaut, les entités préintégrées ont plusieurs valeurs, ce qui signifie que la mémoire du bot conserve toutes les instances identifiées de l’entité.

## <a name="steps"></a>Étapes

### <a name="create-the-model"></a>Création du modèle

1. Dans l’interface utilisateur web, cliquez sur New Model
2. Sous Nom, entrez BuiltInEntities. Cliquez ensuite sur Créer.

### <a name="create-an-entity"></a>Créer une entité

1. Cliquez sur Entités, puis sur Nouvelle entité.
2. Cliquez sur la liste déroulante EntityType et sélectionnez datetimev2.
    - Les options Programmable et Negatable sont désactivées, car elles ne s’appliquent pas aux entités préintégrées.
3. Cliquez sur Créer.

![](../media/tutorial7_entities.PNG)

### <a name="create-two-actions"></a>Créer deux actions

1. Cliquez sur Actions, puis sur Nouvelle action
2. Dans Réponse, tapez « La date est $luis-datetimev2 ».
3. Cliquez sur Créer.

![](../media/tutorial7_actions.PNG)

Ensuite, créez la deuxième action :

1. Cliquez sur Actions, puis sur Nouvelle Action pour en créer une deuxième.
3. Dans Réponse, tapez « Quelle est la date ? ».
4. Dans Entités disqualifiantes, entrez « luis-datetimev2 ».
4. Click Create

![](../media/tutorial7_actions2.PNG)

Vous avez maintenant deux actions.

### <a name="train-the-bot"></a>Former le bot

1. Cliquez sur Boîtes de dialogue d’apprentissage, puis sur Nouvelle boîte de dialogue d’apprentissage.
2. Tapez « Bonjour ».
3. Cliquez sur Attribuer un score aux actions et sélectionnez « Quelle est la date ? ».
2. Entrez « aujourd’hui ». 
    - Notez qu’aujourd’hui est balisé et apparaît dans la deuxième ligne puisqu’il s’agit d’une entité préintégrée et non modifiable.
5. Cliquez sur Attribuer un score aux actions
    - Notez que la date s’affiche désormais dans la section Mémoire de l’entité. 
    - Si vous passez la souris sur la date, vous verrez les données supplémentaires fournies par LUIS, qui sont utilisables et peuvent être manipulées dans le code. 
6. Sélectionnez « La date est $luis-datetimev2 ».
7. Cliquez sur Apprentissage terminé

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Entrées alternatives](./8-alternative-inputs.md)
